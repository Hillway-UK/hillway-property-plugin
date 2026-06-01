# Hillway Property Plugin

UK commercial property toolkit for chartered surveyors and property managers, built by [Hillway](https://hillwayco.uk) (RICS Regulated Firm No. 900798).

Designed for Claude Cowork. Also works in Claude Code.

## What it does

Five flagship skills covering the core surveying and property management workflows:

| Skill                  | Description                                                                                                                                                              |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `property-data`        | UK property intelligence: VOA, HMLR, EPC, flood risk, planning, heritage, transactions, ownership. Returns a structured report with comparable-backed valuation context. |
| `lease-advisory`       | Lease abstraction, rent review timing, break options, repairing obligation analysis, user clauses, alienation, key dates register.                                       |
| `rent-review-analysis` | Comparable evidence collation, ERV analysis, headline vs effective rent, RICS Red Book compliant supporting note.                                                        |
| `service-charge`       | RICS Service Charge Code compliant budget preparation, apportionment methods, year-end reconciliation, dispute handling.                                                 |
| `rics-standards`       | RICS Rules of Conduct, Red Book Global, Service Charge Code, AI Practice Statement reference. Quick lookups for compliance questions.                                    |

Plus slash commands for one-shot reports:

| Command                                  | What it does                                          |
| ---------------------------------------- | ----------------------------------------------------- |
| `/property-report [address or postcode]` | Full property intelligence report, branded PDF output |
| `/lease [path-to-PDF]`                   | Lease abstract and key dates register                 |

## Installation

### Claude Cowork (one toggle)

1. Open Claude
2. Customise → Plugins → Browse plugins → search "Hillway Property"
3. Click Install
4. Connect the MCP servers you want (M365, Slack, Xero are optional)

### Claude Code (CLI)

```bash
claude plugin marketplace add Hillway-UK/hillway-property-plugin
claude plugin install hillway-property@hillway-property-plugin
```

## What you'll need

The plugin works out-of-the-box for property lookups using Hillway-hosted MCPs. For workflows that touch your own data (M365, Slack, Xero), you connect those during install.

| MCP                     | Required? | Notes                                                                                                            |
| ----------------------- | --------- | ---------------------------------------------------------------------------------------------------------------- |
| hillway-voa             | Yes       | Hillway-hosted, free for personal use                                                                            |
| hillway-hmlr            | Yes       | Hillway-hosted, free for free-tier HMLR queries; paid for title-plan downloads (£3/title, charged direct to you) |
| hillway-epc             | Yes       | Hillway-hosted, free                                                                                             |
| hillway-flood           | Yes       | Hillway-hosted, free                                                                                             |
| hillway-planning        | Yes       | Hillway-hosted, free                                                                                             |
| hillway-heritage        | Yes       | Hillway-hosted, free                                                                                             |
| hillway-companies-house | Yes       | Hillway-hosted, free for standard quota                                                                          |
| ms365                   | Optional  | Microsoft-hosted, requires your tenant consent                                                                   |
| slack                   | Optional  | Salesforce-hosted, requires your Slack OAuth                                                                     |
| xero                    | Optional  | Hillway-hosted, requires your Xero OAuth                                                                         |

## Who this is for

- **Chartered surveyors** (RICS registered or RICS Regulated firms) producing valuations, rent reviews, lease advisory work, market reports, service charge management.
- **Property managers** maintaining portfolios, tracking compliance, raising rent and service charge invoices, dealing with arrears.
- **Property funds and asset managers** doing due diligence on acquisitions, monitoring covenant strength of tenants, tracking key dates across a portfolio.
- **Solicitors and accountants** with property-heavy client books needing fast lookups during transactions or at year-end.

## Disclaimer

All AI-assisted outputs are draft work product. You are responsible for review, verification, and final use before any external reliance. This plugin is delivered in accordance with the RICS AI Practice Statement (March 2026).

Hillway Holdings Limited maintains £2,000,000 PI cover with RSA, policy renewable October 2026.

## Support

- Wider implementation services: Hillway Embed and Evolve, from £8,125 plus VAT. See `https://hillwayco.uk/digital`.
- Plugin issues and feature requests: open an issue at `https://github.com/Hillway-UK/hillway-property-plugin/issues`.
- Direct: matt@hillwayco.uk.

## Licence

Apache 2.0. See LICENSE.

Built by Hillway Holdings Limited. Company No. 14319867. RICS Regulated Firm No. 900798.
