# hillway-property-plugin

> Public Cowork plugin for UK commercial property. The free distribution funnel
> into Hillway's paid Embed and Evolve engagements.

## Status

- Public repo, Apache 2.0: `Hillway-UK/hillway-property-plugin`
- 5 skills (property-data, lease-advisory, rent-review-analysis, service-charge, rics-standards), 2 commands (/property-report, /lease)
- All skills sanitised: no internal Supabase IDs, keys, multipliers or orchestration. The full proprietary skills stay in Hillway's internal library.
- Verified installing locally: `hillway-property@hillway-property-plugin v0.1.0`

## Architecture

```
hillway-property/
├── .claude-plugin/plugin.json   manifest
├── .mcp.json                    single hillway-property HTTP endpoint + optional M365/Slack
├── commands/                    /property-report, /lease
├── skills/                      5 public-safe skills routing to the MCP tools
├── README.md
└── CONNECTORS.md
```

The plugin's `.mcp.json` points at `https://mcp.hillwayco.uk/mcp`, served by the
private `Hillway-UK/hillway-property-mcp` HTTP server (Streamable HTTP). Until
that endpoint is deployed, the Hillway team uses the stdio server registered in
Claude at user scope.

## Data path

The plugin and Hillway's internal skills share one resilient data path: the
`hillway-property-mcp` server, which reads cached datasets in Supabase first and
falls back to live government APIs only on a miss. So lookups survive gov
outages (proven: VOA returned live data while the EPC government API was down).

## Install

```bash
claude plugin marketplace add Hillway-UK/hillway-property-plugin
claude plugin install hillway-property@hillway-property-plugin
```

Or in Cowork: Customize, Plugins, Browse, search "Hillway Property", Install.

## Strategy

Free public plugin (this) draws prospects in. Private Embed packs and the Evolve
retainer are the paid layer. Mirrors Anthropic's knowledge-work-plugins pattern.
Apply to the Anthropic Partner Network with CPR as the first reference Embed.

## Related

- `Hillway-UK/hillway-property-mcp` (private) — the MCP servers behind this
- `client-demos/cpr/23-claude-for-small-business-assessment.md` — the assessment that drove this
- Memory: `Project-Hillway-Property-Plugin`, `Project-Hillway-Property-MCP`
