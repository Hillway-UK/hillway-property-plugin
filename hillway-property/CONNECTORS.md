# Connectors

This document explains the MCP servers the Hillway Property plugin uses and how to connect them.

## Hillway Property MCP (one connector, free to start)

One Hillway-hosted endpoint serves all 18 property tools (VOA, EPC, HM Land
Registry ownership and sales transactions, planning, heritage, flood, Companies
House covenant and officers, plus the combined report and leads finder):

| MCP              | Endpoint                       | Data sources                                               |
| ---------------- | ------------------------------ | ---------------------------------------------------------- |
| hillway-property | `https://mcp.hillwayco.uk/mcp` | VOA, EPC, HMLR, planning, heritage, flood, Companies House |

### Sign in to connect

When you connect, Claude takes you to Hillway to sign in (a one-time email link,
no password). Sign-in is what unlocks the connector and takes a few seconds.

| Tier | Price   | What you get                                                                                 |
| ---- | ------- | -------------------------------------------------------------------------------------------- |
| Free | £0      | Rating, floor area, EPC, ownership, planning, heritage, flood and a headline property report |
| Pro  | £49/mo  | Adds sales comparables, RV benchmarks, covenant and director due diligence, full report      |
| Firm | £199/mo | Adds the EPC leads engine and bulk, uncapped                                                 |

See [hillwayco.uk/property/pricing](https://hillwayco.uk/property/pricing). For
firm-wide or API terms, contact matt@hillwayco.uk.

## Optional connectors (your credentials)

These point at your own systems. The plugin works without them but unlocks more workflows when connected.

### Microsoft 365

| Connector | Endpoint                                  | What it gives you                                                                                                                   |
| --------- | ----------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| ms365     | `https://microsoft365.mcp.claude.com/mcp` | OneDrive (lease ingestion), Outlook (compliance certificate sweep), Excel (tenancy schedules), Word (report editing), Teams (calls) |

To connect: during plugin install, follow the OAuth prompt. Your M365 tenant admin must consent on first use. EU/UK admins must also enable the Anthropic sub-processor toggle in Microsoft 365 admin centre.

### Slack

| Connector | Endpoint                    | What it gives you                                                            |
| --------- | --------------------------- | ---------------------------------------------------------------------------- |
| slack     | `https://mcp.slack.com/mcp` | Send compliance reminders, ingest team conversations, post AI-to-AI handoffs |

To connect: install the Salesforce Slack MCP via plugin install OAuth.

### Xero

| Connector | Endpoint                        | What it gives you                                                                                   |
| --------- | ------------------------------- | --------------------------------------------------------------------------------------------------- |
| xero      | `https://mcp.hillwayco.uk/xero` | Rent invoicing, service charge accounting, cash-in reconciliation, tracking categories per property |

To connect: during plugin install, follow the Hillway-hosted Xero OAuth. Your Xero subscription continues to be billed by Xero directly.

## Alternative tools (not currently supported)

The Hillway Property plugin focuses on UK-regulated property data plus M365, Slack, and Xero because that is the stack our delivery clients use. If you use other tools and want first-class support, open an issue.

- Google Workspace alternative to M365 — partial support via the standard Anthropic productivity plugin
- QuickBooks alternative to Xero — not supported; integrate via Zapier if required
- Re-Leased property management software — Phase 2 candidate, not yet shipped
- Property auction APIs (EIG, BiddingWar) — Phase 2 candidate

## How to add custom MCPs

You can extend this plugin by editing your local `.mcp.json` after install, or by forking the plugin and adding your MCPs to the source `.mcp.json`. See the Anthropic plugin docs for details.

## Security and data

- No credentials are stored in plugin files
- All Hillway-hosted MCPs use HTTPS
- All third-party MCPs (M365, Slack) use their vendors' standard OAuth
- Hillway does not log or persist client data beyond the duration of a single request to the Hillway-hosted MCPs
- For full data handling details see https://hillwayco.uk/privacy
