---
name: service-charge
description: Service charge management expertise including RICS Service Charge Code compliance, budget preparation, apportionment methods, and year-end reconciliation.
---

# Service Charge Expertise

## RICS Service Charge Code (3rd Edition)

The RICS Professional Statement on Service Charges in Commercial Property sets mandatory requirements.

### Core Principles

1. **Transparency** - Tenants understand what they're paying for
2. **Fairness** - Costs reasonable and fairly apportioned
3. **Communication** - Clear, timely information provision

### Landlord Obligations

- Provide annual budget before start of service charge year
- Issue year-end reconciliation within 4 months of year-end
- Allow tenant inspection of accounts/invoices on request
- Competitive tendering for contracts over threshold
- Written management agreement with managing agent

### Tenant Rights

- Receive itemised budget and reconciliation
- Query costs within reasonable period
- Inspect supporting documentation
- Challenge unreasonable charges
- Participate in budget meetings (if offered)

## Service Charge Components

### Typical Expenditure Categories

| Category | Description | % of Total |
|----------|-------------|------------|
| Repairs & Maintenance | Building fabric, plant, M&E | 25-35% |
| Utilities | Common parts electricity, water | 10-20% |
| Insurance | Buildings insurance (if charged) | 10-20% |
| Management Fee | Agent fees | 8-12% |
| Cleaning | Common parts, windows | 8-12% |
| Security | Patrols, CCTV, access control | 5-10% |
| Landscaping | Grounds maintenance | 3-8% |
| Health & Safety | Testing, compliance | 2-5% |
| Reserve/Sinking Fund | Future major works | 5-15% |

### Items NOT Usually Recoverable

- Void costs (empty unit contributions)
- Improvements (vs repairs)
- Initial fit-out costs
- Landlord's costs of letting
- Costs arising from landlord's default
- Costs covered by insurance claim

## Apportionment Methods

### 1. Floor Area (Most Common)

```
Tenant Share = (Tenant Area / Total Area) × Total Expenditure
```

Considerations:
- Use Net Internal Area (NIA) for offices
- Use Gross Internal Area (GIA) for industrial/retail
- Measure consistently
- Document measurement basis

### 2. Rateable Value

Used when floor areas uncertain or as lease provides:
```
Tenant Share = (Tenant RV / Total RV) × Total Expenditure
```

### 3. Fixed Percentage

As specified in lease (e.g., "15% of total expenditure")
- Check lease wording carefully
- May need adjustment if building changes

### 4. Weighted Apportionment

Adjusted for:
- Different benefit from services
- Ground floor vs upper floors
- 24/7 access vs standard hours
- Special facilities used

## Budget Preparation

### Annual Budget Process

1. **Review Previous Year** (Month 9-10)
   - Analyse actual vs budget variances
   - Identify cost pressures
   - Note any one-off items

2. **Contractor Review** (Month 10-11)
   - Obtain renewal quotes
   - Benchmark against market
   - Consider re-tendering if appropriate

3. **Draft Budget** (Month 11)
   - Line-by-line build-up
   - Inflation assumptions
   - Planned works provision
   - Reserve fund contribution

4. **Management Review** (Month 11-12)
   - Landlord/client approval
   - Challenge major increases
   - Document assumptions

5. **Issue to Tenants** (Before Year Start)
   - Budget summary
   - Explanatory notes for significant changes
   - On-account payment schedule

### Budget Template

```
SERVICE CHARGE BUDGET [Year]
[Property Name]

EXPENDITURE                          Budget    Prior Year   Change %
------------------------------------------------------------------
REPAIRS & MAINTENANCE
  Building Fabric                    £xx,xxx   £xx,xxx      x%
  Mechanical & Electrical            £xx,xxx   £xx,xxx      x%
  Planned Works                      £xx,xxx   £xx,xxx      x%
  Reactive Repairs                   £xx,xxx   £xx,xxx      x%
                                    --------   --------
  Sub-total                         £xx,xxx   £xx,xxx      x%

UTILITIES
  Electricity                        £xx,xxx   £xx,xxx      x%
  Gas                               £xx,xxx   £xx,xxx      x%
  Water                             £xx,xxx   £xx,xxx      x%
                                    --------   --------
  Sub-total                         £xx,xxx   £xx,xxx      x%

[Continue for all categories]

TOTAL EXPENDITURE                   £xxx,xxx  £xxx,xxx      x%

Less: Income (if any)               (£x,xxx)  (£x,xxx)
                                    --------   --------
NET EXPENDITURE                     £xxx,xxx  £xxx,xxx      x%

APPORTIONMENT
Total Area: xx,xxx sq ft
Rate per sq ft: £x.xx

TENANT SCHEDULE
Tenant          Area (sq ft)    %       Annual Charge
----------------------------------------------------------
Unit 1          x,xxx          xx%      £xx,xxx
Unit 2          x,xxx          xx%      £xx,xxx
Unit 3          x,xxx          xx%      £xx,xxx
----------------------------------------------------------
TOTAL           xx,xxx         100%     £xxx,xxx
```

## Year-End Reconciliation

### Process

1. **Close Accounts** - Final invoices posted
2. **Accrue/Prepay** - Match to service charge year
3. **Reconciliation Statement** - Actual vs budget
4. **Certificate** - Signed reconciliation
5. **Issue to Tenants** - Within 4 months
6. **Balancing Adjustment** - Credit or debit

### Variance Analysis

Report significant variances (typically >10% or >£5k):
- Reason for variance
- Was it foreseeable?
- One-off or ongoing?
- Action to prevent recurrence

## ServCharge Integration

Hillway's ServCharge system (Supabase) provides:
- Automated budget calculations
- AI-generated commentary
- PDF report generation
- Historical benchmarking

Query ServCharge data:
```sql
SELECT * FROM properties WHERE organisation_id = '[org]';
SELECT * FROM budgets WHERE property_id = '[property]';
SELECT * FROM line_items WHERE budget_id = '[budget]';
```

## Common Disputes

### Cost Challenges

1. **"Costs unreasonable"**
   - Benchmark against similar properties
   - Evidence of competitive tendering
   - Justify specification

2. **"Not recoverable under lease"**
   - Check lease SC clause wording
   - Sweeper clause coverage
   - Variation rights

3. **"Apportionment unfair"**
   - Review lease apportionment provisions
   - Check measurement accuracy
   - Consider weighted alternative

### Resolution Options

1. Negotiation
2. RICS Dispute Resolution Service
3. FTT (Residential) / Court (Commercial)
4. Expert Determination (if lease provides)

## Benchmarking Data

### Office Service Charges (UK Average 2024)

| Grade | City Centre | Out of Town |
|-------|-------------|-------------|
| Grade A | £8-12/sq ft | £5-7/sq ft |
| Grade B | £6-9/sq ft | £4-6/sq ft |

### Retail Service Charges

| Type | Range |
|------|-------|
| Shopping Centre | £12-25/sq ft |
| Retail Park | £3-6/sq ft |
| High Street | £4-8/sq ft |

### Industrial Service Charges

| Type | Range |
|------|-------|
| Multi-let Estate | £1-3/sq ft |
| Distribution Park | £0.75-2/sq ft |
