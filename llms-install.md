# Installing the Suparanku MCP Server

Suparanku's MCP server is **remote** — there is nothing to build or run locally.

## Prerequisites

1. A Suparanku account on a paid plan (Starter, Business, or Enterprise): https://suparanku.com/ja/pricing/
2. A Personal Access Token: in the Suparanku app open **Settings → MCP & API tokens** and create a token (read-only, or read + write to act on recommendations).

## Connection details

- Transport: Streamable HTTP
- URL: `https://app.suparanku.com/api/mcp/v1`
- Auth header: `Authorization: Bearer <YOUR_TOKEN>`

## Cline

Add to `cline_mcp_settings.json` (MCP Servers → Configure):

```json
{
  "mcpServers": {
    "suparanku": {
      "url": "https://app.suparanku.com/api/mcp/v1",
      "headers": { "Authorization": "Bearer YOUR_TOKEN" },
      "disabled": false
    }
  }
}
```

## Verify

After connecting, call `suparanku_help` (overview + full tool list) and then `suparanku_list_companies` — every other tool needs a `company_id` from it. If a company shows `mcp_access: "none"`, enable MCP access for it in the Suparanku app.

If the account has no company yet — or you need another one — `suparanku_create_company` creates a workspace with its first brand and stops there: no onboarding audit is run, so you set the context up yourself (brand profile, aliases, domains, topics, prompts). It requires a read + write token scoped to all companies, admin or owner on a company with full MCP access, and a remaining company-creation allowance.

Full generated tool reference: https://app.suparanku.com/docs/mcp
