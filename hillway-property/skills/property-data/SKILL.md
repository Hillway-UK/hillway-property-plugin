---
name: property-data
description: UK property data toolkit. Auto-triggers when the user asks about any UK property, postcode, EPC, floor area, rateable value, market rent, capital value, ownership, flood risk, planning, transactions, or comparables. Routes to the Hillway Property MCP tools.
---

# UK Property Data Toolkit

This skill answers UK commercial property questions by routing to the Hillway
Property MCP tools. The MCP servers read Hillway's cached datasets first, so
lookups keep working even when the live government APIs are down.

## When to activate

Auto-trigger when the user mentions:

- A UK postcode or property address
- EPC ratings or energy performance
- Floor area, square footage, GIA, NIA
- Rateable value, business rates, VOA
- Market rent, rent per sq ft, ERV, capital value, yield
- Property ownership, who owns it, title
- Flood risk or flood zone
- Planning applications, conservation area, listed building
- Transaction prices, what it sold for
- Comparable evidence, comps
- Tenant covenant strength

## Tools to use

| Need                                           | Tool                                 |
| ---------------------------------------------- | ------------------------------------ |
| Rateable value, floor area for a postcode      | `voa_by_postcode`                    |
| Rateable value, floor area for a street        | `voa_by_street`                      |
| Rent and price-per-sqft benchmark for a sector | `voa_benchmark_postcode`             |
| EPC energy rating for a postcode               | `epc_search_postcode`                |
| EPC for a specific building                    | `epc_search_address`                 |
| EPC across both registers                      | `epc_lookup_both_registers`          |
| Tenant covenant, company ownership             | Companies House tools (if connected) |

Each VOA result includes rateable value, floor area in sqm and sqft, use
description and an accommodation breakdown. The `voa_benchmark_postcode` tool
returns sample size, min, max and median rateable value and price per sqft,
plus a use-type breakdown, which is useful as comparable evidence context for
rent reviews and valuations.

Each EPC result carries a `source` field showing whether it came from the
Hillway cache or the live EPC service.

## How to produce a property report

1. Take the address or postcode from the user.
2. Call `voa_by_postcode` (or `voa_by_street`) for rating and floor area.
3. Call `epc_search_postcode` for energy performance.
4. Call `voa_benchmark_postcode` for comparable context if rent or value is in
   scope.
5. Add Companies House covenant detail if ownership or tenant strength matters
   and the tools are connected.
6. Synthesise into a structured summary. Flag any item that needs human
   verification before external reliance, per the RICS AI Practice Statement.

## Disclaimer

All outputs are draft work product. The user is responsible for verification
and final use before any external reliance. This skill is delivered in
accordance with the RICS AI Practice Statement (March 2026).

Built by Hillway Holdings Limited. RICS Regulated Firm No. 900798.
