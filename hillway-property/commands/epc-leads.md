---
description: Find commercial properties with poor EPC ratings (energy retrofit, solar and lighting leads) in a UK council area.
argument-hint: "[council name]"
---

# /epc-leads

Build a leads list of commercial properties with poor EPC bands in a council
area. These are the prime targets for energy retrofit, solar and lighting
upgrades, and the properties most exposed to MEES minimum-standard rules.

## Usage

```
/epc-leads Rotherham
/epc-leads Sheffield
```

## What it does

Call the `epc_leads` tool with the council name. By default it targets bands E,
F and G (the poor performers). It returns each property's address, postcode,
energy band, registration date and UPRN, plus the total available count.

Then:

1. Report the total count and the band split.
2. Show the first page of leads.
3. Offer to page through the rest, or to enrich each lead with floor area
   (`voa_by_postcode`) and ownership (`ownership_by_postcode`) so the list is
   contactable and prioritised by retrofit value.

## Notes

- A single council query is capped at 1,000 results per band by the government
  API. For large councils, query band by band, or split by postcode district.
- To export a full multi-council CSV pack, the Hillway delivery team uses the
  `leads-export` script in the property MCP server.

## Related

- `property_report` for a full single-property overview
- `ownership_by_proprietor` to map a target's whole portfolio
