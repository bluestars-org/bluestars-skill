---
name: bluestars
description: >-
  Work with a Bluestars customer dashboard through the Bluestars MCP server:
  conversation analytics, usage/billing, BSR customer & visitor segmentation
  insights, postcode-to-segment lookups, BSR color-variant rewriting of copy,
  and the public REST API. Use when the user asks about their Bluestars data,
  widget conversations, BSR segments/personas, campaign results, rewriting copy
  per BSR segment, or Bluestars API usage.
---

<!-- GENERATED FILE, do not edit plugin/SKILL.md directly.
     Source: plugin/SKILL.template.md + src/lib/mcp/tool-catalog.ts.
     Regenerate: npm run generate-skill -w bs-sdk (runs in build:plugins too). -->

# Bluestars

Bluestars is a decision layer on top of a marketing stack: an embeddable AI
assistant (chat widget) plus BSR® psychographic segmentation of customers and
website visitors. This skill works against the **Bluestars MCP server**, which
exposes the same data the customer dashboard shows.

## Connecting

The MCP server lives at `https://bluestars.app/api/mcp` (Streamable HTTP).
Authentication is OAuth 2.1 with PKCE; the server supports both CIMD (URL
client IDs) and Dynamic Client Registration, so any modern MCP client can
connect without pre-registration. On first use you will be sent to the
Bluestars consent page: log in with your dashboard account (magic link) and
approve. Tokens carry exactly your dashboard permissions: tools for sections
you cannot see in the dashboard return a permission error.

## Orientation

Always call `get_account_info` first. It returns the tenant (customer
account) the session is scoped to, your accessible sections, and whether the
tenant has a public API key provisioned.

## Tools

| Tool                          | What it returns                                                                                                                    | Needs section |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| `get_account_info`            | Tenant, user, accessible sections, API-key status                                                                                  | none          |
| `list_tenants`                | Every tenant this login can act on (multi-tenant users & admins)                                                                   | none          |
| `get_dashboard_stats`         | KPIs: conversations, surveys, visitors, opens, BSR donut, daily series                                                             | analytics     |
| `get_usage_summary`           | Plan tier, credits used/available, seats, API add-on usage                                                                         | none          |
| `list_conversations`          | Paginated widget conversations with counts + first message                                                                         | analytics     |
| `get_conversation`            | Full transcript of one conversation                                                                                                | analytics     |
| `get_bsr_customer_insights`   | BSR distribution + socio-demographics of the customer's own base (postcode upload)                                                 | insights      |
| `get_bsr_visitor_insights`    | BSR distribution + socio-demographics of website visitors                                                                          | insights      |
| `get_bsr_report`              | Latest AI-generated BSR report (markdown)                                                                                          | insights      |
| `lookup_postcode_bsr_segment` | Dutch postcode(s) → BSR segment via the public API (metered on the tenant's API key)                                               | insights      |
| `get_public_api_info`         | Base URL, auth and endpoint map of the public REST API                                                                             | none          |
| `get_widget_install_snippet`  | Widget embed snippet to add to the site (plain <script> + a GTM/document.write variant)                                            | none          |
| `generate_content`            | Write web copy per BSR segment or a blog article, from the customer's pages or from a brief (needs studio EDIT rights + mcp:write) | studio        |
| `rewrite_bsr_variants`        | Rewrite copy into a variant per BSR segment of the active pack (needs studio EDIT rights + mcp:write)                              | studio        |

## Typical workflows

- **"How is my assistant doing?"** → `get_dashboard_stats` (range `7d` or
  `30d`), then `list_conversations` for concrete examples; quote the first
  user messages, not IDs.
- **"What do my customers look like?"** → `get_bsr_customer_insights` +
  `get_bsr_report`; compare against `get_bsr_visitor_insights` to spot gaps
  between the customer base and site traffic.
- **"Segment this address list"** → `lookup_postcode_bsr_segment` with up to
  200 postcodes per call. This is metered (1 credit per non-cached lookup):
  tell the user before running large batches.
- **"Rewrite this copy for each BSR segment"** → `rewrite_bsr_variants` with
  the copy (text, HTML or Markdown); pass `colors` to target specific
  segments. Needs studio EDIT rights and an `mcp:write` connection; every
  variant it generates also warms the live content-swap cache.
- **Direct REST integration** → `get_public_api_info` for the endpoint map;
  the API key itself is only visible on the dashboard's Settings page.

## Multiple tenants

Every tenant-scoped tool takes an optional `tenant` argument (slug or id).
Omit it for the account's default tenant. When `get_account_info` reports
`accessibleTenantCount > 1`, call `list_tenants` and pass the right slug.
Bluestars admins reach every tenant; regular users reach tenants where their
email has an account, always with that tenant's own permission set.

## Notes

- Most tools are read-only. `lookup_postcode_bsr_segment` is a metered public
  API lookup that can record usage credits, and `rewrite_bsr_variants`
  generates content variants and can warm the visitor-facing content cache.
  Neither performs destructive admin actions.
- Ranges use Europe/Amsterdam calendar days.
- BSR colors (bsr4 pack): rood = vrijheid, groen = veiligheid, blauw =
  controle, geel = harmonie. Larger packs (bsr8, bsr7lei) return their own
  slugs: take persona names from tool output, never invent them.
