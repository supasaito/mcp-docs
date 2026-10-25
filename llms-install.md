# Installing the Supasaito MCP Server

Supasaito’s MCP server is remote — there is nothing to build or run locally.

## Prerequisites

1. A [Supasaito account](https://supasaito.com/ja/signup/). MCP is included on all plans, including Free. Cost-bearing operations still require an appropriate plan and quota.
2. An OAuth connection, or a Personal Access Token created in **Settings → MCP & API tokens**. Choose the type — **Personal** (acts as you) or **Agent** (its own name and avatar; use one per independent agent) — then select permitted companies/brands and read/write access; creation, deletion and Service permissions are separate choices. The type cannot be changed later.

## Connection details

- Transport: Streamable HTTP
- URL: `https://platform.supasaito.com/api/mcp`
- PAT header: `Authorization: Bearer <YOUR_TOKEN>`
- OAuth: use the client’s sign-in flow and review the requested access.

Keep real tokens out of repositories, documents and shared screenshots. Store them in the client’s local settings or supported secret store.

### Claude (claude.ai / Desktop)

Open Settings → Connectors → Add custom connector, enter the endpoint and complete OAuth sign-in. Availability and labels depend on the client’s account and version.

### Claude Code

For OAuth:

```bash
claude mcp add --transport http supasaito https://platform.supasaito.com/api/mcp
```

Open `/mcp` in Claude Code to authenticate. For PAT authentication:

```bash
claude mcp add --transport http supasaito https://platform.supasaito.com/api/mcp --header "Authorization: Bearer YOUR_TOKEN"
```

See [Claude Code’s MCP documentation](https://code.claude.com/docs/en/mcp).

### Cursor

Add to `.cursor/mcp.json` (keep a real token in local, untracked configuration):

```json
{
  "mcpServers": {
    "supasaito": {
      "url": "https://platform.supasaito.com/api/mcp",
      "headers": { "Authorization": "Bearer YOUR_TOKEN" }
    }
  }
}
```

### Cline

Add to `cline_mcp_settings.json` (MCP Servers → Configure):

```json
{
  "mcpServers": {
    "supasaito": {
      "type": "streamableHttp",
      "url": "https://platform.supasaito.com/api/mcp",
      "headers": { "Authorization": "Bearer YOUR_TOKEN" },
      "disabled": false
    }
  }
}
```

## Verify access

Call `supasaito_help`, then `supasaito_list_companies`. Company-scoped calls require a returned `company_id`. Pass an explicit own `brand_id` when managing multiple brands: selected-brand connections can omit it only when one permitted brand is available; all-brand connections default to the oldest own brand. The brand selected in the app does not change MCP scope.

Access combines membership, role, connection selections, company policy and plan. If a company is missing or reports `mcp_access: "none"`, review these settings with its owner/admin. Reconnecting does not grant additional permissions by itself.

For business context call `supasaito_get_business_context`; for existing measurements, `supasaito_get_dynamics_summary` reports changes between scans. No data yet is different from a failed connection. Use the [agent workflow](./agent-workflow.md) for workspace creation, context, campaigns, tasks and publishing.

## Versions

`/api/mcp` serves the current major version, v5, with `supasaito_*` tool names. Pin `/api/mcp/v5` when a stable major contract is needed. v5 replaces `supasaito_get_business_profile` with `supasaito_get_business_context`; v5.13 removes the market-map editing, market research and real-demand tools (the market research is now a business-context document). Inspect the server’s advertised catalog before using tools added in later minor versions.

Pinned v1–v3 endpoints retain legacy `suparanku_*` names and supported compatibility behavior. `/api/mcp/v1` sunsets on **2026-12-05**; retired workflow writes require migration to the current endpoint. Different major versions do not have identical tool contracts.

## Connection troubleshooting

- **OAuth sign-in or connector setup fails:** check that the URL is exactly the endpoint above, then retry authentication. If the client retains a failed connection, remove it and add it again. This can refresh local authorization state; it cannot fix a server-side registration error. If it still fails, send support the client/version, time and timezone, failing step and any displayed error/reference code. Do not send tokens or authorization codes.
- **Unauthorized / expired credentials:** reauthenticate OAuth or replace a revoked/expired PAT.
- **Forbidden / a company or brand is missing:** review membership and connection permissions; do not keep retrying an operation outside the granted scope.
- **Unknown tool:** check the pinned major version and refresh the client’s tool catalog. Use the names and input schema the server advertises.
- **Rate limit or version conflict:** follow the server’s retry delay; for conflicts reread the current document/task and merge before retrying.

Canonical [generated tool reference](https://platform.supasaito.com/docs/mcp) · [agent workflow](./agent-workflow.md) · support@supasaito.com
