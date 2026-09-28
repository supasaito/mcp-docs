# Supasaito MCP Server

Remote [MCP (Model Context Protocol)](https://modelcontextprotocol.io) server for **[Supasaito (スーパーサイト)](https://supasaito.com/ja/platform/)** — the AI representation management system: see and change how AI represents your business.

Connect any MCP-compatible AI client or agent (Claude, Claude Code, Cursor, Cline, custom agents) to your brand's AI-visibility data: how ChatGPT, Claude, Gemini and Google AI Overviews talk about your brand — and act on it.

- **Endpoint:** `https://platform.supasaito.com/api/mcp` (remote, Streamable HTTP)
- **Tool reference (generated, always current):** https://platform.supasaito.com/docs/mcp
- **Auth:** Personal Access Token (app → Settings → MCP & API tokens) or OAuth 2.1
- **Availability:** all paid plans (Starter, Business, Enterprise), read + write. A company created over MCP starts on the free plan with MCP switched on — reads and the zero-cost writes that shape the measured set (brand, topics, prompts, competitors, sources) work immediately, while scans, site audits, content briefs and market research wait for a paid plan.

## Quick start

You need a Supasaito account on a paid plan and a token. Create one in the app: **Settings → MCP & API tokens** (choose read-only or read + write scope; destructive operations additionally require an allow-destructive scope).

### Claude (claude.ai / Desktop)

Settings → Connectors → **Add custom connector** → paste `https://platform.supasaito.com/api/mcp` and complete the OAuth sign-in.

### Claude Code

```bash
claude mcp add --transport http supasaito https://platform.supasaito.com/api/mcp --header "Authorization: Bearer YOUR_TOKEN"
```

### Cursor

`.cursor/mcp.json`:

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

See [`llms-install.md`](./llms-install.md).

## What you can do

First call `supasaito_list_companies`; company-scoped tools take a `company_id` from it. Pass an explicit `brand_id` when managing several brands. Then:

- **Analytics (latest scan)** — `supasaito_get_metrics`: visibility / position / sentiment / competitor share, grouped by topic, engine, competitor or prompt; trends or any past scan.
- **Dynamics (scan over scan)** — `supasaito_get_dynamics_summary` answers "what changed since the last scan" in one call: the headline numbers with their move, every topic ranked with its change and who leads it, every tracked brand with its change. `supasaito_get_visibility_dynamics` draws the movement itself — visibility, average position or sentiment, cut by brand, topic or assistant. `supasaito_get_sources_dynamics` does the same for what the assistants read. Every point carries how many runs it stands on, how many recommendations were published since the previous scan, and a flag when the measured prompt set changed in between — so a rise across a changed measurement is never reported as a clean result.
- **Sources & citations** — which domains AI answers cite in your market (`supasaito_get_sources`), and where competitors get cited while you don't (`supasaito_get_competitor_source_gap`) — a ready publication target list.
- **Recommendations & content briefs** — prioritized recommendations (`supasaito_list_recommendations`), full briefs per publication (`supasaito_get_content_brief`), mark published URLs for re-measurement (`supasaito_mark_recommendation_published`).
- **Site audit (SSPS)** — AI-readiness score for the whole site and per page (`supasaito_get_site_audit`, `supasaito_get_page_audit`), technical fix briefs with ready-to-paste artifacts, autonomous fix loop via `supasaito_run_site_fast_audit`.
- **Search & AI traffic** — everything we hold from the brand's own Search Console and GA4: the real queries people typed (`supasaito_get_search_queries`), per-page search performance, indexing status with the full URL-Inspection diagnosis, daily series, traffic split per AI assistant and per channel (`supasaito_get_traffic_channels`), one page's full proof loop from published to first cited (`supasaito_get_page_traffic_detail`), and the connection status that tells "not connected" apart from "no data yet" (`supasaito_get_google_integration_status`).
- **Management (write)** — brands, topics, prompts, competitors, source categories, tracked URLs; trigger scans, site audits, market research and PDF reports.
- **New workspaces (write)** — `supasaito_create_company` requires an explicit `free_audit: "run" | "skip"` decision. `run` starts the free audit; poll `supasaito_get_free_audit_progress` and wait for completion before writing context. `skip` creates the workspace without spending its free audit. Company creation requires write scope, portfolio-derived admin/owner eligibility and remaining creation allowance. Scope the token to all companies if it must reach newly created ones. `supasaito_create_brand` adds a brand to an existing paid company and crawls it; it does not run the company’s free audit.
- **Context and publishing work** — shared company metadata, versioned business documents, channel/placement setup, uploaded assets, campaigns and custom tasks. Required business documents are `brand.business` and `brand.customers`; read document guidance before writing.
- **Parallel agents** — `supasaito_actions_claim_next_task` atomically assigns eligible work or review. A task contains its own submission and review; write and review calls use fencing versions. Use distinct credentials for independent participants. Company rate limits are shared, not multiplied by agent count. Creation tools accept `request_id` for safe retries; list and long-text reads are paginated.

`/api/mcp` always serves the current version (v4); agents that hard-code tool names can pin `/api/mcp/v4`. Suparanku was renamed Supasaito: v4 names every tool `supasaito_*` (resources `supasaito://`), while the pinned `/api/mcp/v1`–`/api/mcp/v3` keep the `suparanku_*` names. Inputs, results and permissions are the same. The v1 endpoint sunsets on **2026-12-05**. Retired v1 workflow writes return migration instructions. Connect to `/api/mcp` and read current tasks before resuming work. See the [generated reference](https://platform.supasaito.com/docs/mcp) and [agent workflow](./agent-workflow.md).


Example agent prompts:

> "Pull this week's open recommendations for my brand and summarize the top 3 by impact."
> "Which domains cite my competitors but not me? Group by category."
> "Run a fast site audit, then list the technical fixes with their briefs."
> "Which Google queries do we already get impressions for but never rank on page one?"
> "What changed since the last scan, and which of it followed something we published?"

## Security

- Tokens are tenant-scoped: a token reaches only the companies it was issued for, with your role's permissions. Revocable at any time by a company owner/admin.
- Read vs write is a token-level scope; destructive tools additionally require an allow-destructive scope **and** `confirm: true` per call.
- Data residency: Tokyo. Details: [supasaito.com/ja/platform/security](https://supasaito.com/ja/platform/security/)

## Links

- Product: https://supasaito.com/ja/platform/ · MCP page: https://supasaito.com/ja/platform/mcp/
- Sign up: https://supasaito.com/ja/signup/
- Privacy policy: https://supasaito.com/ja/legal/platform/privacy/
- Support: support@supasaito.com

---

**日本語**: Supasaito (スーパーサイト) のリモートMCPサーバーです。MCP対応のAIクライアント／エージェント（Claude、Claude Code、Cursor、Clineなど）から、自社ブランドのAI可視性データ（可視性・順位・センチメント・競合・引用ソース）の読み取りと、改善提案・コンテンツブリーフ・サイト監査の操作ができます。エンドポイントは `https://platform.supasaito.com/api/mcp`、認証はPersonal Access Token（アプリの設定 → MCP・APIトークン）またはOAuth 2.1。有料プラン（Starter以上）でご利用いただけます。新しい企業ワークスペースやブランドの作成もMCPから行えます（企業の作成時に、無料の初回監査を実行するかどうかを明示的に選択します）。Search Console・GA4と連携している場合は、実際の検索クエリ、インデックス状況、AIアシスタント別の流入まで取得できます。直近スキャンの数値だけでなく、スキャンごとの推移（自社と競合、トピック別、AIアシスタント別）も取得できます。各ポイントには測定回数、前回スキャン以降に公開した施策の件数、測定対象のプロンプト構成が変わったかどうかの印が付きます。v4からツール名は `supasaito_*` です（バージョンを固定した `/api/mcp/v1`〜`/api/mcp/v3` は従来の `suparanku_*` のまま利用できます）。ツール一覧（自動生成・常に最新）: https://platform.supasaito.com/docs/mcp

## Brand scope and publication flow

Pass company_id and an explicit own brand_id when managing multiple brands.
If brand_id is omitted, MCP uses the oldest own brand of that company, independently
of the brand selected in the app. Invalid explicit own-brand selectors are rejected.
get_business_profile additionally accepts a competitor brand as the profile target.

Set each campaign publication's language and link_role during preparation:
original, rewrite or crosslink. Adaptations use their assigned language, which may
differ from the brand's default. Rewrites and crosslinks require an included original
and wait for every included original to be published; excluded originals do not block.
Use the current task's review policy. Publishing has no outgoing review: save the
HTTP/HTTPS URL, then submit that publication task. A repeated save preserves the first
publication time; a timeout never calls for a duplicate external post. Read actual
consumed quotas, including market research, through supasaito_get_company_usage.

## Funnels and campaigns

Create a funnel with its first step, the activator: active contact such as a consultation, trial, telephone call or meeting. Later steps are optional. Each step can have its own context document. Link one or more funnels to a campaign; launching requires enabled funnels with described activators. Agents receive the selected context in their task briefs. Configure funnels and steps, write documents and link campaigns through the current tools; normal role, resource and review permissions apply.

Version 2 retains reads and compatible operations. Reconnect to `/api/mcp` for funnel configuration. Its sunset date will be announced.

## Service conversations

Service channels support reading messages, searching a channel and sending messages or threaded replies. Enable Service access explicitly for each connection and select channels; sending has a separate permission. Existing connections do not gain access automatically. These permissions do not grant access to business data or tasks. Service eligibility follows company membership and the company’s Service setting, independently of the AI subscription. See the [generated reference](https://platform.supasaito.com/docs/mcp) for the three `supasaito_service_*` tools.
