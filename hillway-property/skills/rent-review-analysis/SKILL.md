---
name: rent-review-analysis
description: Calculates rent review positions using comparable evidence, ERV analysis, and headline vs effective rent adjustments. Triggers when user asks about rent reviews, market rent, ERV, comparable evidence, or rental value.
---

# Rent Review Analysis Skill

## Workflow

### Step 1: Gather Property Details
- Property address, floor area (NIA/GIA), current rent
- Lease terms: review date, review pattern (upward only / open market)
- Use class and specification

### Step 2: Collect Comparable Evidence
Search for recent lettings and rent reviews in the locality:
```
WebSearch "[location] commercial lettings [use class] [year]"
WebSearch "[location] rent review [property type] comparable"
```

For each comparable, record:
- Address, floor area, rent agreed, date, incentives
- Adjust for: size, specification, location, lease terms, date

### Step 3: Analyse Comparables

**Hierarchy of Evidence (RICS):**
1. Open market lettings (strongest)
2. Lease renewals
3. Rent review settlements
4. Independent expert / arbitrator determinations
5. Court decisions

**Adjustments:**
- Time: index to review date
- Size: quantum allowance for larger units
- Specification: grade A vs B vs C adjustment
- Location: micro-location factors
- Incentives: convert headline to effective rent

### Step 4: Calculate ERV

```
Headline Rent → less rent-free equivalent → Effective Rent
Effective Rent ÷ Floor Area = Rate per sq ft
```

Rent-free conversion:
```
Effective Rent = Headline × (Lease Term - Rent Free) / Lease Term
```

### Step 5: Formulate Position

| Perspective | Approach |
|-------------|----------|
| Landlord | Weight higher comparables, argue specification premium |
| Tenant | Weight lower comparables, argue incentive adjustments |
| Independent | Weighted average with reasoned adjustments |

### Step 6: Sensitivity Analysis

| Basis | Rate (per sq ft) | Annual Rent | vs Current |
|-------|-------------------|-------------|------------|
| Low | £X | £Y | +Z% |
| Mid (recommended) | £A | £B | +C% |
| High | £D | £E | +F% |

### Step 7: Negotiation Strategy
- Opening position
- Walk-away point
- Likely settlement range
- Timeline and process recommendations

## Output
Structured analysis showing all comparable evidence, adjustments, workings, and clear recommendation with confidence level.
