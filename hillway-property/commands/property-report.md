---
description: Produce a full UK commercial property intelligence report for an address or postcode. Runs all property data lookups in parallel and outputs a structured, branded report ready for client delivery.
argument-hint: "[address or postcode]"
---

# /property-report

Produce a comprehensive UK property intelligence report.

## Usage

```
/property-report 35 Moorgate, Rotherham S60 2AG
/property-report S60 2AG
/property-report Stewart House, Moorgate Road, Rotherham
```

## What it does

1. **Geocode** the input via OS / postcodes.io
2. **Fan-out in parallel** across 7+ data sources:
   - VOA (rateable value, floor area, market rent benchmark)
   - HMLR (registered title, proprietor, CCOD)
   - EPC (energy performance, recommendations)
   - Environment Agency (flood zone 2 and 3, surface water)
   - Planning (applications past 5 years, conservation, listed)
   - Heritage (Historic England, Cadw, HES)
   - Companies House (proprietor covenant, charges, directors)
3. **Synthesise** the findings against the `property-data` skill rules
4. **Produce** a structured markdown report with sections:
   - Property summary
   - Title and ownership
   - Floor area and rateable value
   - EPC and energy
   - Flood risk
   - Planning history
   - Heritage status
   - Proprietor covenant assessment
   - Comparable evidence (if requested)
   - RICS health flags

## Output

Markdown by default. Add `--pdf` to produce a Hillway-branded PDF using Chrome headless. Add `--brand=cpr` to use a fork brand (CPR red `#BD1622`, charcoal, Calibri) if you have a Hillway-Embed deployment.

## Limitations

- UK addresses only (England and Wales primary, Scotland and Northern Ireland partial)
- HMLR title-plan downloads cost £3 per title and are charged direct to your HMLR account
- Some local authority planning portals are slower than others; expect 30 to 90 seconds end to end
- Property-data skill provides full guidance on data sources, edge cases, and verification rules

## Related

- `/lease` — abstract a lease PDF
- `/voa` — VOA-only lookup
- `/epc` — EPC-only lookup
- `/companies-house-watch` — set up a tenant watchlist
