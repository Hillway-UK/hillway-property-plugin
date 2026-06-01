# Connectors

This document explains the MCP servers the Hillway Property plugin uses and how to connect them.

## Hillway-hosted MCPs (no setup needed)

These run on Hillway infrastructure and are free to call for any user of the plugin. No credentials required from you.

| MCP                     | Endpoint                                   | Data source                   | Rate limit |
| ----------------------- | ------------------------------------------ | ----------------------------- | ---------- |
| hillway-voa             | `https://mcp.hillwayco.uk/voa`             | VOA Rating List               | 60 req/min |
| hillway-hmlr            | `https://mcp.hillwayco.uk/hmlr`            | HM Land Registry              | 30 req/min |
| hillway-epc             | `https://mcp.hillwayco.uk/epc`             | Open Data Communities (DLUHC) | 60 req/min |
| hillway-flood           | `https://mcp.hillwayco.uk/flood`           | Environment Agency ArcGIS     | 60 req/min |
| hillway-planning        | `https://mcp.hillwayco.uk/planning`        | planning.data.gov.uk + PlanIt | 60 req/min |
| hillway-heritage        | `https://mcp.hillwayco.uk/heritage`        | Historic England + Cadw + HES | 60 req/min |
| hillway-companies-house | `https://mcp.hillwayco.uk/companies-house` | Companies House live API      | 60 req/min |

For higher-rate usage, contact matt@hillwayco.uk to discuss commercial terms.

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
