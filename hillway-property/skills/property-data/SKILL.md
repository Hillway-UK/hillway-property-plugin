---
name: property-data
description: UK property data toolkit — auto-triggers when user asks about any UK property, postcode, EPC, floor area, rateable value, market rent, capital value, ownership, flood risk, planning, transactions, or comparables. Provides comparable-backed valuations via RealiQ + direct API access to 14+ data sources.
---

# UK Property Data Toolkit (v2, 23 Apr 2026)

## When to Activate

Auto-trigger when the user mentions:

- A UK postcode (e.g. "S1 2BJ", "SW1A 1AA")
- Property addresses in England/Wales
- EPC ratings, energy performance
- Floor area, square footage, GIA, NIA
- Rateable value, business rates, VOA
- Market rent, rent per sq ft, ERV, capital value, yield
- Property ownership, who owns, CCOD, title
- Flood risk, flood zone
- Planning applications, conservation area, listed building
- Transaction prices, what did it sell for, price paid
- Comparable evidence, comps
- Covenant strength, tenant check
- Transport accessibility, catchment demographics, broadband, crime stats

## Canonical Execution Order (parallel where possible)

For a full `/property-report` or `/property-lookup`, run in parallel **after** geocoding:

```
Stage 1 (sequential)    → Google Geocoding (needed for lat/lng)
Stage 2 (parallel fan-out) →
    ├── EPC (domestic + non-domestic)
    ├── VOA (Supabase)
    ├── CCOD (Supabase)
    ├── Land Registry Price Paid (SPARQL)
    ├── Flood — Zone 2 + Zone 3 (EA ArcGIS)
    ├── Planning — planning.data.gov.uk (primary)
    ├── Planning — PlanIt (supplementary)
    ├── Heritage (Historic England ArcGIS — conservation, listed, scheduled)
    ├── RealiQ comparables (Bearer auth)
    ├── ONS Census + IMD
    ├── Ofcom Connected Nations
    ├── data.police.uk crime
    ├── NaPTAN transport
    └── Google Solar + Air Quality (optional)
Stage 3 (synthesis) →
    ├── Market rent (see "Market Rent — v2 logic")
    ├── Capital value (see "Capital Value — v2 logic")
    ├── Covenant snapshot (Companies House deep lookup on owner + any tenants)
    └── Branded PDF render (Playwright → open on macOS)
```

---

## Data Sources

### 1. EPC Data (Energy Performance Certificates)

**API**: `https://epc.opendatacommunities.org/api/v1/{domestic|non-domestic}/search`
**Auth**: `$EPC_API_KEY` (Basic auth, key as username, empty password)
**Returns**: Rating (A-G), score, floor area, property type, built form, heating, walls, roof, windows, fuel type, inspection date
**Usage**:

```bash
curl -s -H "Accept: application/json" \
  -H "Authorization: Basic $(echo -n "matt@hillwayco.uk:$EPC_API_KEY" | base64)" \
  "https://epc.opendatacommunities.org/api/v1/non-domestic/search?postcode={POSTCODE}&size=20"
```

Run BOTH domestic and non-domestic. Commercial properties can have either.

---

### 2. VOA Rating List (1.8M commercial properties)

**Source**: Supabase table `voa_properties` on project `cxykibwgpmezdanafull`
**Tool**: `mcp__supabase__execute_sql`
**Returns**: Floor area (sqm & sqft), rateable value, description code/text, address

```sql
SELECT * FROM voa_properties
WHERE postcode = '{POSTCODE}' AND rateable_value > 0 AND list_year = 2023;
```

**⚠️ Important:** The RV × sector factor approach below is the **fallback only**. Primary market-rent derivation is via RealiQ comparables — see "Market Rent — v2 logic".

Legacy multipliers (fallback when < 3 comparables exist within 1 mile):

- Industrial (CW, IF, IW, IG, EW, LS): **1.286** (NIA / GIA = GIA)
- Office (CO): **1.06** (NIA)
- Retail (CS, CR): **0.995** (NIA)
- Other: **1.107** (NIA)

These are 2021 Antecedent Valuation Date factors and are now 2+ years stale — apply time-decay when using (see v2 logic below).

---

### 3. CCOD Ownership

**Source**: Supabase table `ccod_ownership` on project `cxykibwgpmezdanafull`
**Refresh**: Monthly bulk download via `$CCOD_API_KEY`
**Returns**: Title number, tenure, proprietor name, category, company registration number

```sql
SELECT * FROM ccod_ownership WHERE postcode = '{POSTCODE}';
```

If the proprietor name contains a company registration number or looks corporate, **always** pipe it through Companies House deep lookup (§11 below) for the Covenant Snapshot.

Refresh pipeline (monthly / on-demand):

```bash
/Users/matt/Projects/property-data/scripts/refresh-ccod.sh
```

---

### 4. Land Registry Price Paid

**API**: SPARQL at `https://landregistry.data.gov.uk/landregistry/query`
**Auth**: None (free open data)
**Returns**: Transaction price, date, property type, tenure, new build status
**Scope:** Residential only. For commercial transaction evidence, use **RealiQ comparables (§10)**.

---

### 5. Flood Risk (Environment Agency)

**APIs** (all free, no auth):

- Zone 3: `https://environment.data.gov.uk/arcgis/rest/services/EA/FloodMapForPlanningRiversAndSeaFloodZone3/MapServer/0/query?geometry={LNG},{LAT}&geometryType=esriGeometryPoint&spatialRel=esriSpatialRelIntersects&outFields=flood_zone&f=geojson`
- Zone 2: same URL pattern with `FloodZone2`
- Flood Areas (live alerts): `https://environment.data.gov.uk/flood-monitoring/id/floodAreas?lat={LAT}&long={LNG}&dist=1`
- Surface Water Risk: `https://environment.data.gov.uk/arcgis/rest/services/EA/RiskOfFloodingFromSurfaceWater/MapServer/0/query?geometry={LNG},{LAT}&geometryType=esriGeometryPoint&f=geojson`
- Reservoir Risk: `https://environment.data.gov.uk/arcgis/rest/services/EA/RiskOfFloodingFromReservoirs/MapServer/0/query?geometry={LNG},{LAT}&geometryType=esriGeometryPoint&f=geojson`

Report flood risk as a combined matrix: rivers/sea (Zone 2/3), surface water (High/Medium/Low/Very Low), reservoir (Yes/No).

---

### 6. Planning (DUAL-QUERY — primary + supplementary)

**Primary: planning.data.gov.uk** (official, 337 LPAs, no auth)

```bash
# By postcode
curl -s "https://www.planning.data.gov.uk/entity.json?q={POSTCODE}&limit=50"

# By lat/lng + radius (500m)
curl -s "https://www.planning.data.gov.uk/entity.json?longitude={LNG}&latitude={LAT}&distance=500&limit=50"
```

Returns structured entities: dataset, name, start-date, geometry, UPRN, decision-date. Covers planning applications + designations (conservation, listed, TPO, flood zones, brownfield, AONB).

**Caveat:** LPAs are "not currently required to share to this specification" per Gov.uk docs — coverage patchy. Always dual-query.

**Supplementary: PlanIt** (unofficial scraper, decent LPA coverage)

```
https://www.planit.org.uk/api/applics/json?lat={LAT}&lng={LNG}&radius=100&recent=365&limit=30
```

Returns: reference, proposal text, status, decision, type.

**Synthesis rule:** Merge both by `reference`. If Gov.uk has it, use that. If only PlanIt, include with `source: planit` caveat.

---

### 7. Heritage — Historic England ArcGIS

APIs (free, no auth; use `inSR=4326` for WGS84):

- **Conservation Areas**: `https://services-eu1.arcgis.com/ZOdPfBS3aqqDYPUQ/ArcGIS/rest/services/Conservation_Areas/FeatureServer/0/query?geometry={LNG},{LAT}&geometryType=esriGeometryPoint&inSR=4326&spatialRel=esriSpatialRelIntersects&distance=50&units=esriSRUnit_Meter&outFields=NAME,DATE_OF_DE,LPA&f=json`
- **Listed Buildings**: `https://services-eu1.arcgis.com/ZOdPfBS3aqqDYPUQ/ArcGIS/rest/services/National_Heritage_List_for_England_NHLE_v02_VIEW/FeatureServer/0/query?geometry={LNG},{LAT}&geometryType=esriGeometryPoint&inSR=4326&spatialRel=esriSpatialRelIntersects&distance=25&units=esriSRUnit_Meter&outFields=Name,Grade,ListEntry,ListDate&f=json`
- **Scheduled Monuments**: NHLE layer 6
- **Registered Parks & Gardens**: NHLE layer 7

Grade matters: Grade I = 15–25% valuation haircut typical; Grade II\* = 10–15%; Grade II = 5–10%.

---

### 8. Google Geocoding / Solar / Air Quality

- Geocoding: `https://maps.googleapis.com/maps/api/geocode/json?address={ADDRESS}&key=$GOOGLE_MAPS_API_KEY`
- Solar: `https://solar.googleapis.com/v1/buildingInsights:findClosest?location.latitude={LAT}&location.longitude={LNG}&key=$GOOGLE_MAPS_API_KEY`
- Air Quality: POST to `https://airquality.googleapis.com/v1/currentConditions:lookup?key=$GOOGLE_MAPS_API_KEY` with `{"location":{"latitude":LAT,"longitude":LNG}}`

---

### 9. Companies House (Covenant Snapshot)

**API**: `https://api.company-information.service.gov.uk`
**Auth**: `$COMPANIES_HOUSE_API_KEY` (Basic auth, key as username, empty password)

For every corporate proprietor from CCOD + every corporate tenant identified from lease data:

```bash
BASE="https://api.company-information.service.gov.uk"
AUTH="Authorization: Basic $(echo -n "$COMPANIES_HOUSE_API_KEY:" | base64)"

# 1. Profile — status, incorp date, SIC codes, registered office
curl -s -H "$AUTH" "$BASE/company/{NUMBER}"

# 2. Officers — current directors, nationality, appointed/resigned
curl -s -H "$AUTH" "$BASE/company/{NUMBER}/officers"

# 3. Filing history — recent accounts, confirmation statements, charges
curl -s -H "$AUTH" "$BASE/company/{NUMBER}/filing-history?items_per_page=10"

# 4. Charges register — mortgages, floating charges, satisfied/outstanding
curl -s -H "$AUTH" "$BASE/company/{NUMBER}/charges"

# 5. PSCs — persons with significant control (25%+, voting rights)
curl -s -H "$AUTH" "$BASE/company/{NUMBER}/persons-with-significant-control"
```

**Covenant Snapshot output (section in report):**

- Status (Active / Dissolved / Liquidation) — red flag any non-active
- Accounts: last-filed date, overdue?, micro/small/medium/large
- Charges: outstanding vs satisfied, latest charge date, floating vs fixed
- Officers: current count, directors turnover in last 12 months
- PSC control: concentrated (single PSC > 75%) or distributed
- Rating heuristic: `A1 / B1 / C1 / D1 / E1` based on filing currency + charge load + director churn

---

### 10. RealiQ Comparables (NEW — transaction-backed valuation)

**Endpoint**: `https://realiq.uk/api/mcp` (JSON-RPC 2.0)
**Auth**: `Authorization: Bearer $REALIQ_MCP_KEY` (nc\_\* format)
**Tools exposed**:

- `search_comparables(sector, city, date_range)`
- `get_property(id)` — includes top 10 comparables auto-matched
- `search_properties(postcode, sector, price_range, yield_range)`
- `search_tenancies(property_id)`

**Dataset**: 923 comparables, UK-wide, AI-extracted from transaction sources. Fields:
`achieved_price, achieved_rent, rent_per_sqft, price_per_sqft, floor_area_sqft, transaction_date, postcode, property_type, yield_percent, confidence_score`.

**Usage for market rent / capital value:**

```bash
# Via MCP (preferred when supabase/realiq MCP loaded in Claude):
# Claude will call mcp__realiq__search_comparables automatically.

# Direct JSON-RPC fallback:
curl -s -X POST https://realiq.uk/api/mcp \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $REALIQ_MCP_KEY" \
  -d '{
    "jsonrpc":"2.0","id":1,"method":"tools/call",
    "params":{
      "name":"search_comparables",
      "arguments":{"sector":"office","city":"Sheffield","date_range":"24m"}
    }
  }'
```

Filter client-side to comparables within **1 mile** of target (use Haversine on postcode centroid) and within **24 months** of today.

---

### 11. ONS Census 2021 + Indices of Multiple Deprivation (IMD)

**No auth, free.**

- Census 2021 postcode lookup → output area (OA) → demographics:
  `https://api.beta.ons.gov.uk/v1/datasets/TS001/editions/2021/versions/3/observations?geography={OA_CODE}` (population by age)
  Full catalogue: `https://api.beta.ons.gov.uk/v1/datasets`
- Postcode → OA mapping: `https://api.postcodes.io/postcodes/{POSTCODE}` (free)
- IMD 2019 by LSOA: `https://api.beta.ons.gov.uk/v1/datasets/imd/editions/2019/versions/1/observations?geography={LSOA}` or GOV.UK bulk file

**Report fields**: population density, age skew, median household income (proxied via occupation band), IMD decile (1 = most deprived, 10 = least), tenure mix (owner-occupied / social / private rented).

**Use for:** retail catchment, mixed-use schemes, ESG reporting, business rates relief eligibility checks.

---

### 12. Ofcom Connected Nations (broadband + mobile)

**No auth, free** (downloadable postcode-level CSV).

Bulk file: `https://www.ofcom.org.uk/research-and-data/multi-sector-research/infrastructure-research/connected-nations-2024` (pick the postcode-level CSV)

**Cached locally** at `/Users/matt/Projects/property-data/data/ofcom-connected-nations-2024.csv.gz` (run `scripts/refresh-ofcom.sh` yearly).

**Returns per postcode**:

- Max advertised download speed (Mbps)
- Max upload speed
- Full-fibre availability (Y/N)
- 4G coverage (all major operators)
- 5G outdoor/indoor coverage

**Use for:** office and retail due diligence ("will my team have gigabit?"), data-centre siting, hybrid-working pitches.

---

### 13. data.police.uk Crime Stats

**No auth, free.**

```
https://data.police.uk/api/crimes-street/all-crime?lat={LAT}&lng={LNG}&date=YYYY-MM
```

Returns all street-level crimes within 1-mile radius for the month. Also: neighbourhood team info, stop-and-search data, crime category breakdowns.

**Report fields** (last 12 months rolling):

- Total crimes / 1000 pop
- Top 3 crime types (usually anti-social behaviour, violent crime, burglary)
- Trend vs previous 12 months
- Comparison to LPA average

**Use for:** retail pitches (shoplifting / ASB exposure), occupier duty-of-care briefs, insurance cost framing.

---

### 14. NaPTAN Transport Accessibility

**No auth, free.**

- NaPTAN CSV: https://naptan.api.dft.gov.uk/v1/access-nodes (national stop list — bus, rail, tram, ferry)
- Postcode → nearest stop distance via spatial query after geocoding
- TfL PTAL (London only): https://api.tfl.gov.uk/PtlCoverage (London-authority-level PTAL scores 0–6b)
- National Rail OpenLDBWS: https://www.nationalrail.co.uk/developers (requires free account)

**Report fields**:

- Nearest bus stop (distance, services count)
- Nearest rail station (distance, name, frequency)
- Walking time to major local amenities
- PTAL score (London only — 0 = very poor, 6b = excellent)

**Use for:** office pitches (commute / talent-pool story), occupier accessibility advice, site-suitability matrices.

---

## Market Rent — v2 logic (CANONICAL)

Run this every time a report needs an ERV figure. Do **not** default to the RV-multiplier only.

```
Input: postcode, VOA records (RV, floor area, sector code)

Step 1 — Query RealiQ:
  mcp__realiq__search_comparables(
    sector=map_from_voa_code(description_code),
    city=postcode_district_town,
    date_range="24m"
  )

Step 2 — Filter to evidence set:
  comps = [c for c in results
           if haversine(c.postcode, target_postcode) < 1.0   # miles
           and (today - c.transaction_date).days < 730        # 24 months
           and c.confidence_score > 0.4]

Step 3 — If len(comps) >= 3:
  median_rent_per_sqft = median([c.rent_per_sqft for c in comps if c.rent_per_sqft])
  rent_pa = median_rent_per_sqft * floor_area_sqft
  confidence = "comparable-backed"
  evidence_count = len(comps)

Step 4 — Else (fallback, RV-based with time decay):
  sector_factor = MARKET_RENT_FACTORS[description_code]  # 1.286 / 1.06 / 0.995 / 1.107
  years_since_2021 = (today - date(2021, 4, 1)).days / 365.25
  decay = {
      "industrial": 1.05 ** years_since_2021,   # +5% pa compound
      "office":     0.98 ** years_since_2021,   # -2% pa compound
      "retail":     0.97 ** years_since_2021,   # -3% pa compound
      "other":      1.02 ** years_since_2021,   # +2% pa compound
  }[sector_of(description_code)]
  rent_pa = RV * sector_factor * decay
  confidence = "RV-derived (no local comparables)"
  evidence_count = 0

Return: rent_pa, rent_per_sqft = rent_pa / floor_area, confidence, evidence_count
```

**Always show confidence + evidence_count in the report.** Prospects with leasing experience trust a comparable-backed figure; they discount a multiplier estimate.

---

## Capital Value — v2 logic (CANONICAL)

```
Input: rent_pa (from above), sector, comparables (from RealiQ)

Step 1 — If comparables with yield data exist:
  median_yield = median([c.yield_percent for c in comps if c.yield_percent > 0])
  capital_value = rent_pa / (median_yield / 100)
  confidence = "yield-backed"

Step 2 — Else (sector default yields, as of Apr 2026 — refresh quarterly):
  default_yields = {
      "office-prime":       4.75,
      "office-secondary":   7.50,
      "industrial-prime":   5.25,
      "industrial-secondary": 7.00,
      "retail-prime":       6.00,
      "retail-secondary":   9.00,
      "retail-highstreet":  8.00,
      "mixed":              6.50,
  }
  capital_value = rent_pa / (default_yield / 100)
  confidence = "sector-default yield"

Return: capital_value, yield_used, confidence
```

**Report presentation:** always show as a range: `value_low = value × 0.9`, `value_high = value × 1.1`, with the median as the point estimate.

---

## Supabase Project Reference

**Project ID**: `cxykibwgpmezdanafull`
**URL**: `https://cxykibwgpmezdanafull.supabase.co`

### Key Tables

| Table                     | Records   | Content                                       |
| ------------------------- | --------- | --------------------------------------------- |
| `voa_properties`          | ~1.8M     | VOA 2023 Rating List — all commercial         |
| `voa_market_rent_factors` | 4         | Sector uplift factors (legacy — see v2 logic) |
| `ccod_ownership`          | ~4.4M     | Corporate/overseas ownership                  |
| `properties`              | User data | Steer AI user portfolio                       |
| `property_lookups`        | User data | Cached lookup results (24h)                   |

### Useful VOA SQL Patterns

Largest properties in an area:

```sql
SELECT address_number_name, street, town, postcode, description_text,
       rateable_value, total_floor_area_sqft
FROM voa_properties
WHERE postcode_district = 'S1' AND rateable_value > 0 AND list_year = 2023
ORDER BY total_floor_area_sqft DESC NULLS LAST
LIMIT 20;
```

All offices in a postcode:

```sql
SELECT * FROM voa_properties
WHERE postcode_district = 'S1' AND description_code = 'CO'
  AND rateable_value > 0 AND list_year = 2023
ORDER BY rateable_value DESC;
```

---

## Slash Commands Available

| Command                      | Use Case                                                         |
| ---------------------------- | ---------------------------------------------------------------- |
| `/property-lookup [address]` | Full 14-source intelligence report (sequential)                  |
| `/property-report [address]` | Same as above, branded HTML output                               |
| `/property-swarm [address]`  | Parallel fan-out (8 specialist sub-agents)                       |
| `/pitch-demo [postcode]`     | Pre-cached live-demo for pitch meetings (see `pitch-demo` skill) |
| `/epc [postcode]`            | Quick EPC lookup                                                 |
| `/voa [postcode]`            | VOA floor area, RV, v2 market rent                               |
| `/comparables [postcode]`    | RealiQ transaction evidence (primary) + VOA (supplementary)      |
| `/ownership [postcode]`      | CCOD + Companies House covenant snapshot                         |
| `/flood-check [address]`     | Full flood risk (rivers, surface water, reservoir)               |
| `/planning [address]`        | Dual-query planning (Gov.uk + PlanIt) + heritage                 |
| `/land-registry [postcode]`  | Price paid transactions                                          |

---

## MCP Servers Used

- **supabase** (VOA, CCOD, Steer portfolio) — same project `cxykibwgpmezdanafull`
- **realiq** (comparables, yields, tenancies) — `https://realiq.uk/api/mcp`, Bearer auth
- **playwright** (HTML → PDF render)
- **memory** (session state, caching)
- **google-workspace** (Drive uploads of generated PDFs)

All four should be loaded in both Claude Desktop and Claude Code for parity. See `~/Library/Application Support/Claude/claude_desktop_config.json`.

---

## Integration with Existing Skills

- **rent-review-analysis**: use RealiQ comparables for primary evidence, VOA for contextual floor-area + RV
- **investment-yield**: use Capital Value v2 logic with RealiQ yields
- **lease-advisory**: CCOD + Companies House for landlord covenant, tenancies for existing lease terms
- **service-charge**: VOA for apportionment floor areas, EPC for ESG compliance obligations
- **dcf-property**: feed rent_pa + capital_value from v2 logic into the DCF model
- **lookalike**: CCOD + Companies House + SIC codes for similar-firm prospecting

---

## Changelog

- **2026-04-23 — v2** — Added RealiQ comparable-backed market rent + capital value. Added planning.data.gov.uk primary planning source. Added Companies House Covenant Snapshot section. Added ONS, Ofcom, data.police.uk, NaPTAN. Documented canonical execution order (parallel fan-out). Time-decay correction for legacy RV multipliers.
- **2026-04 (original)** — 10-source toolkit (EPC, VOA, CCOD, Land Registry, Flood, PlanIt, Heritage, Google triplet).
