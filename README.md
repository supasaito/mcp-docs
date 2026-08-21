# Suparanku MCP Server

Remote [MCP (Model Context Protocol)](https://modelcontextprotocol.io) server for **[Suparanku（スーパーランク）](https://suparanku.com)** — the AI representation management system: see and change how AI represents your business.

Connect any MCP-compatible AI client or agent (Claude, Claude Code, Cursor, Cline, custom agents) to your brand's AI-visibility data: how ChatGPT, Claude, Gemini and Google AI Overviews talk about your brand — and act on it.

- **Endpoint:** `https://app.suparanku.com/api/mcp/v1` (remote, Streamable HTTP)
- **Tool reference (generated, always current):** https://app.suparanku.com/docs/mcp
- **Auth:** Personal Access Token (app → Settings → MCP & API tokens) or OAuth 2.1
- **Availability:** all paid plans (Starter, Business, Enterprise), read + write. A company created over MCP starts on the free plan with MCP switched on — reads and the zero-cost writes that shape the measured set (brand, topics, prompts, competitors, sources) work immediately, while scans, site audits, content briefs and market research wait for a paid plan.

## Quick start

You need a Suparanku account on a paid plan and a token. Create one in the app: **Settings → MCP & API tokens** (choose read-only or read + write scope; destructive operations additionally require an allow-destructive scope).

### Claude (claude.ai / Desktop)

Settings → Connectors → **Add custom connector** → paste `https://app.suparanku.com/api/mcp/v1` and complete the OAuth sign-in.

### Claude Code

```bash
claude mcp add --transport http suparanku https://app.suparanku.com/api/mcp/v1 --header "Authorization: Bearer YOUR_TOKEN"
```

### Cursor

`.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "suparanku": {
      "url": "https://app.suparanku.com/api/mcp/v1",
      "headers": { "Authorization": "Bearer YOUR_TOKEN" }
    }
  }
}
```

### Cline

See [`llms-install.md`](./llms-install.md).

## What you can do

First call `suparanku_list_companies` (every other tool takes a `company_id` from it), then:

- **Analytics** — `suparanku_get_metrics`: visibility / position / sentiment / competitor share, grouped by topic, engine, competitor or prompt; trends or any past scan.
- **Sources & citations** — which domains AI answers cite in your market (`suparanku_get_sources`), and where competitors get cited while you don't (`suparanku_get_competitor_source_gap`) — a ready publication target list.
- **Recommendations & content briefs** — prioritized PLAYBOOK recommendations (`suparanku_list_recommendations`), full briefs per publication (`suparanku_get_content_brief`), mark published URLs for re-measurement (`suparanku_mark_recommendation_published`).
- **Site audit (SRPS)** — AI-readiness score for the whole site and per page (`suparanku_get_site_audit`, `suparanku_get_page_audit`), technical fix briefs with ready-to-paste artifacts, autonomous fix loop via `suparanku_run_site_fast_audit`.
- **Search & AI traffic** — everything we hold from the brand's own Search Console and GA4: the real queries people typed (`suparanku_get_search_queries`), per-page search performance, indexing status with the full URL-Inspection diagnosis, daily series, traffic split per AI assistant and per channel (`suparanku_get_traffic_channels`), one page's full proof loop from published to first cited (`suparanku_get_page_traffic_detail`), and the connection status that tells "not connected" apart from "no data yet" (`suparanku_get_google_integration_status`).
- **Management (write)** — brands, topics, prompts, competitors, source categories, tracked URLs; trigger scans, site audits, market research and PDF reports.
- **New workspaces (write)** — `suparanku_create_company` creates a company with its first brand, `suparanku_create_brand` adds a brand to an existing one. Neither runs the free onboarding audit: the entity is created and nothing else starts, so an agent sets the context up itself instead of waiting on an audit it did not ask for. Company creation needs admin/owner on a company with full MCP access, a token scoped to all companies, and a remaining company-creation allowance (1 per user by default — ask us to raise it).

Example agent prompts:

> "Pull this week's open recommendations for my brand and summarize the top 3 by impact."
> "Which domains cite my competitors but not me? Group by category."
> "Run a fast site audit, then list the technical fixes with their briefs."
> "Which Google queries do we already get impressions for but never rank on page one?"

## Security

- Tokens are tenant-scoped: a token reaches only the companies it was issued for, with your role's permissions. Revocable at any time by a company owner/admin.
- Read vs write is a token-level scope; destructive tools additionally require an allow-destructive scope **and** `confirm: true` per call.
- Data residency: Tokyo. Details: [suparanku.com/ja/security](https://suparanku.com/ja/security/)

## Links

- Product: https://suparanku.com · MCP page: https://suparanku.com/ja/mcp/
- Free AI brand audit: https://app.suparanku.com/ja/signup
- Blog: [Using the MCP server to pull recommendations automatically](https://suparanku.com/ja/blog/suparanku-mcp-pull-recommendations/)
- Support: support@suparanku.com

---

**日本語**: SuparankuのリモートMCPサーバーです。MCP対応のAIクライアント／エージェント（Claude、Claude Code、Cursor、Clineなど）から、自社ブランドのAI可視性データ（可視性・順位・センチメント・競合・引用ソース）の読み取りと、改善提案・コンテンツブリーフ・サイト監査の操作ができます。エンドポイントは `https://app.suparanku.com/api/mcp/v1`、認証はPersonal Access Token（アプリの設定 → MCP・APIトークン）またはOAuth 2.1。有料プラン（Starter以上）でご利用いただけます。新しい企業ワークスペースやブランドの作成もMCPから行えます（無料の初回監査は実行されず、必要な処理はご自身のタイミングで開始できます）。Search Console・GA4と連携している場合は、実際の検索クエリ、インデックス状況、AIアシスタント別の流入まで取得できます。ツール一覧（自動生成・常に最新）: https://app.suparanku.com/docs/mcp
