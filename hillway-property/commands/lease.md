---
description: Abstract a commercial lease PDF into a structured key-dates register, rent profile, repairing obligations summary, and risk flag schedule. Built for chartered surveyors and property managers.
argument-hint: "[path to PDF or URL]"
---

# /lease

Abstract a UK commercial lease PDF.

## Usage

```
/lease ~/Desktop/Stewart-House-First-Floor-Lease.pdf
/lease https://hillwayco.uk/sample/lease.pdf
/lease (then drag a PDF in)
```

## What it does

1. **Ingest** the PDF (handles scanned PDFs via OCR fallback)
2. **Extract** lease fundamentals against the `lease-advisory` skill:
   - Parties (landlord, tenant, guarantor)
   - Premises and demise
   - Term, commencement, expiry, contractual break dates
   - Rent profile (initial, review dates, mechanism)
   - Rent review type (open market, RPI/CPI, fixed, hybrid)
   - User clause
   - Alienation (assignment, underletting, change of control)
   - Repairing obligations (full repairing and insuring, limited, schedule of condition)
   - Insurance
   - Service charge mechanism
   - Forfeiture
   - Heads of terms compliance check
3. **Produce** a structured output:
   - One-page abstract
   - Key dates register (Markdown table, ready to import to Xero/Outlook/Asana)
   - Risk flag schedule (anything unusual, onerous, or non-standard)
   - Suggested management diary (next 18 months)

## Output

Markdown by default. Add `--xlsx` for an Excel key dates register. Add `--brand=cpr` for a branded fork.

## Verification

Every extracted item carries a click-through reference to the page and clause in the source PDF. Hillway recommends a human verification step on every output before external reliance, per the RICS AI Practice Statement.

## Limitations

- Optimised for English/Welsh commercial leases; Scottish leases need extra checks against the Land Reform Act
- Handwritten annotations are extracted but flagged for verification
- Side letters must be loaded separately
- Lease-advisory skill provides full guidance on extraction rules and edge cases

## Related

- `/property-report` — full property intelligence
- `/rent-review-analysis` — comparable-backed rent review position
- `/service-charge` — service charge code compliance
