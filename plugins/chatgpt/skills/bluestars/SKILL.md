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
     Source: plugin/SKILL.template.md + src/lib/mcp/tool-catalog.ts
     + src/lib/content-platform/guidance/flows.ts.
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

Always call `get_started` first. It returns who you are on the account, its
plan, the setup checklist with the next step, and the flows the account can
use. Lead the user through any open setup step before anything else.
`get_account_info` adds the sections you can see and whether the tenant has a
public API key provisioned.

## Flows

Each flow is also an MCP prompt (a slash command in clients that show them),
and `get_workflow_guide` returns its steps.

- **`onboarding`**: Work through the open setup steps with the user, one at a time, explaining why each matters. (`get_started`, `set_website`, `set_site_type`, `get_widget_install_snippet`, `check_script_install`, `upload_customer_data`, `get_site_languages`, `confirm_site_languages`, `connect_google`, `check_google_access`)
- **`brand_setup`**: Collect the brand's own material, condense it into the guidelines every piece of content follows. (`save_knowledge`, `add_knowledge_reference`, `get_brand_guidelines_instructions`, `update_brand_guidelines`, `get_brand_guidelines`)
- **`create_content`**: Write one piece per BSR segment with Bluestars' instructions, and save it to the library. (`get_content_instructions`, `get_library_item`, `save_to_library`)
- **`bulk_content`**: The create_content flow in a loop, for a list of products, pages or campaigns. (`get_content_instructions`, `save_to_library`)
- **`update_content`**: Find a piece in the library and write a new version of it. (`search_library`, `get_library_item`, `get_content_instructions`, `save_to_library`, `update_library_item`, `archive_library_item`)
- **`capture_knowledge`**: When the user states something reusable (a rule, a fact, a decision), offer to save it. (`search_knowledge`, `save_knowledge`, `get_knowledge_entry`, `archive_knowledge_entry`)
- **`use_global_knowledge`**: Look things up in the knowledge banks Bluestars shared with this account. (`list_knowledge_banks`, `search_knowledge`)

## Tools

| Tool                                | What it returns                                                                                                                                                                                       | Needs section |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| `get_account_info`                  | Tenant, user, accessible sections, API-key status                                                                                                                                                     | none          |
| `list_tenants`                      | Every tenant this login can act on (multi-tenant users & admins)                                                                                                                                      | none          |
| `get_dashboard_stats`               | KPIs: conversations, surveys, visitors, opens, BSR donut, daily series                                                                                                                                | analytics     |
| `get_site_statistics`               | Traffic per funnel step, traffic sources, what visitors searched for in the site's own search box, the events visitors triggered and the conversion rate per BSR segment, every row split per segment | analytics     |
| `get_google_performance`            | Traffic source, bounce rate, engagement time and campaign cost per BSR segment from the customer's linked Google accounts                                                                             | analytics     |
| `get_usage_summary`                 | Plan tier, credits used/available, seats, API add-on usage                                                                                                                                            | none          |
| `list_conversations`                | Paginated widget conversations with counts + first message                                                                                                                                            | analytics     |
| `get_conversation`                  | Full transcript of one conversation                                                                                                                                                                   | analytics     |
| `get_bsr_customer_insights`         | BSR distribution + socio-demographics of the customer's own base (postcode upload)                                                                                                                    | insights      |
| `get_bsr_visitor_insights`          | BSR distribution + socio-demographics of website visitors                                                                                                                                             | insights      |
| `get_bsr_report`                    | Latest AI-generated BSR report (markdown)                                                                                                                                                             | insights      |
| `lookup_postcode_bsr_segment`       | Dutch postcode(s) → BSR segment via the public API (metered on the tenant's API key)                                                                                                                  | insights      |
| `get_public_api_info`               | Base URL, auth and endpoint map of the public REST API                                                                                                                                                | none          |
| `generate_content`                  | Write web copy per BSR segment or a blog article, from the customer's pages or from a brief (needs studio EDIT rights + mcp:write)                                                                    | studio        |
| `generate_email`                    | Write an email per BSR segment (subjects, preheaders, body, CTA, hero brief, attributed links) (needs studio EDIT rights + mcp:write)                                                                 | studio        |
| `rewrite_bsr_variants`              | Rewrite copy into a variant per BSR segment of the active pack (needs studio EDIT rights + mcp:write)                                                                                                 | studio        |
| `generate_audience_list`            | Build a postcode-area audience list where a BSR segment dominates, optionally limited to provinces (needs studio EDIT rights + mcp:write; charges tenant credits)                                     | studio        |
| `get_started`                       | Who you are on this account, its plan, and the setup checklist with the next step (call this first)                                                                                                   | none          |
| `get_workflow_guide`                | The steps of a flow (onboarding, brand_setup, create_content, ...), in order                                                                                                                          | none          |
| `set_website`                       | Store the account's website address (team managers)                                                                                                                                                   | none          |
| `set_site_type`                     | Say whether the site sells online; a site that does not skips the customer-data step                                                                                                                  | insights      |
| `get_widget_install_snippet`        | Widget embed snippet to add to the site (plain <script> + a Google Tag Manager variant)                                                                                                               | none          |
| `check_script_install`              | Check the website's homepage for this account's measurement script                                                                                                                                    | insights      |
| `upload_customer_data`              | Upload the customer's postcodes (and optionally products, amounts, dates) as CSV in chunks: start, chunk, preview, finalize                                                                           | insights      |
| `get_site_languages`                | The languages of the website, and with detect a suggestion read off the site                                                                                                                          | none          |
| `confirm_site_languages`            | Confirm the website's languages: the main language and each language's home address (team managers)                                                                                                   | none          |
| `connect_google`                    | Store the Google Analytics property id and/or Google Ads customer id, and get the steps to give Bluestars read access (team managers, paid plan)                                                      | analytics     |
| `check_google_access`               | Check that Bluestars can read the connected Google Analytics property and Ads account                                                                                                                 | analytics     |
| `list_knowledge_banks`              | The account's own knowledge bank and the shared banks it was granted                                                                                                                                  | none          |
| `search_knowledge`                  | Search the knowledge bank by text, type, tag or bank                                                                                                                                                  | none          |
| `get_knowledge_entry`               | One entry in full: body, references and version history                                                                                                                                               | none          |
| `save_knowledge`                    | Create a knowledge entry, or update one with base_version (asks the user first; needs edit rights on the knowledge bank)                                                                              | none          |
| `create_upload_url`                 | A short-lived URL to PUT a large file to, for add_knowledge_reference                                                                                                                                 | none          |
| `add_knowledge_reference`           | Attach a web page, an image or a document to a knowledge entry                                                                                                                                        | none          |
| `archive_knowledge_entry`           | Archive an entry (soft: its history stays; ask the user first)                                                                                                                                        | none          |
| `restore_knowledge_entry`           | Bring back an archived entry, or the content of an earlier version (ask the user first)                                                                                                               | none          |
| `get_brand_guidelines`              | The account's brand guidelines: the brand prompt every assistant follows                                                                                                                              | none          |
| `get_brand_guidelines_instructions` | How to condense brand sources into the brand guidelines (before update_brand_guidelines)                                                                                                              | none          |
| `update_brand_guidelines`           | Save a new version of the brand guidelines (asks the user first; needs the brand right)                                                                                                               | none          |
| `get_content_instructions`          | How to write a piece of content for this account, per BSR segment: call it for EVERY piece, send the etag                                                                                             | none          |
| `save_to_library`                   | Save what you wrote with get_content_instructions: a new item, or a new version with item_id + base_version                                                                                           | none          |
| `search_library`                    | Find saved content by text, type, category, external id, segment or author                                                                                                                            | none          |
| `get_library_item`                  | One saved piece: every variant, its audit and its versions                                                                                                                                            | none          |
| `update_library_item`               | Change an item's title, categories, status or external id (never its content)                                                                                                                         | none          |
| `archive_library_item`              | Archive an item (soft: its history stays; ask the user first)                                                                                                                                         | none          |
| `list_content_categories`           | The account's own categories for its content (campaigns, product lines, topics)                                                                                                                       | none          |
| `create_content_category`           | Add a category the user agreed to (ask first)                                                                                                                                                         | none          |
| `update_content_category`           | Rename, re-parent or archive a category                                                                                                                                                               | none          |

## Typical workflows

- **"How is my assistant doing?"** → `get_dashboard_stats` (range `7d` or
  `30d`), then `list_conversations` for concrete examples; quote the first
  user messages, not IDs.
- **"Which products does a segment look at or buy?"** → `get_site_statistics`.
  The `detail` funnel step holds the product pages with their per-segment
  split, `assistant.product_clicked` the products clicked in the assistant,
  and `purchase_item` the ones actually bought, with revenue. Lead with the
  `standout` segment on a row, never with the raw counts.
- **"Where do visitors drop out?"** → `get_site_statistics` and read
  `pageTypes` as a funnel (home → category → detail → checkout) per segment.
- **"Where does a segment come from, how engaged is it, what does it cost to
  reach?"** → `get_google_performance`: the customer's own Google Analytics
  (channel and source with sessions, bounce rate and engagement time per
  segment) and Google Ads (campaign cost, CPC and conversions per segment).
  Check `connected` first; when an account is not linked, point the user to
  Settings > Google Analytics and Google Ads instead of guessing. A segment
  row counts the sessions in which that segment was measured, so the
  segments never add up to `all`.
- **"What is the conversion per segment?"** → `get_site_statistics` and read
  `conversions`: per goal the conversion rate of the recognised visitors and
  per segment its own rate with an index against that recognised average
  (1.2 = converts 20% better). Answer with those rates, one line per segment,
  the recognised average next to them, never a site-wide rate (unplaced
  visitors see the fallback colour, so that compares a colour with itself); a rate
  marked `thin` is noise, not a difference. Segment splits on every row are
  shares of the recognised visitors, the dashboard's own basis: report
  percentages, never counts, and never state or estimate how many visitors
  were or were not recognised.
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
