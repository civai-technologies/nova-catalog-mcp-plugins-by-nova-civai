---
name: content_pipeline_agent
description: Nova content-pipeline-agent MCP — read before preview_document / tools/call.
---

# Content Pipeline Agent MCP — operator guide

Read this resource before calling tools on **Content Pipeline Agent** (`https://nova.civai.co/mcp/agents/content-pipeline-agent`).

## Auth and scope

| Item | Detail |
|------|--------|
| **Auth** | Programmatic Nova API key JWT — `Authorization: Bearer <token>` or `X-API-Key` |
| **Setup** | https://nova.civai.co/api-keys |
| **Owner scope** | Every tool runs as the signed-in Nova account email, against that owner's content products (`data/content_products/{owner}/`). |
| **Not in scope** | Product config itself (pipeline_type, publish_destinations, enabling the pipeline) — that's **Product Admin** (`product_admin__create_product` / `update_product`). This agent only runs the pipeline on an already-configured product. |

**Tool names** are namespaced: `content_pipeline_agent__<action>`.

**Mutating tools** (`generate_content`, `publish_content`, `index_content`, `delete_opportunities`) require **Yes/No approval** in MCP hosts that support elicitation. `trigger_scan` and `expand_seeds` run without approval — they only stage candidate content, nothing is published.

---

## Mental model

```
Nova owner (API key)
  └── Content Pipeline Agent MCP
        └── content_pipeline_agent__*  → scan-trends → seeds → expand-seeds →
                                          opportunities → generate → drafts →
                                          publish → index
```

This is the execution surface behind `/api/content/*` — the same lifecycle the Content tab's "pipeline" toggle on Product Admin enables, but there's no dashboard UI for actually running it yet. This agent is that missing surface.

**product_id** — copy verbatim from `product_admin__list_products` / `get_product`. The product must have `content_enabled: true` and a `pipeline_type` set, or every action here returns a 409.

Most actions start an async job and return a `job_id` immediately — poll `get_job_status { job_id }` for the result.

---

## Typical flow

1. `trigger_scan { product_id }` → `job_id`. Poll `get_job_status` until done.
2. `list_seeds { product_id }` — review what was found.
3. `expand_seeds { product_id, seed_ids }` → `job_id`. Poll until done.
4. `list_opportunities { product_id }` — review staged opportunities; `delete_opportunities` to prune ones not worth generating.
5. `generate_content { product_id, opportunity_ids }` (approval) → `job_id`. Poll until done.
6. `list_drafts { product_id }` — review generated articles before publishing.
7. `publish_content { product_id, article_ids }` (approval) → `job_id`. Publishes to the product's configured `publish_destinations` (webhook and/or a connected X account, set via Product Admin). Pass `public_urls` manually only if the product has no destination configured yet (deprecated path).
8. `index_content { product_id, urls }` (approval) if the product doesn't have `auto_index_on_publish` set — submits the sitemap to Google Search Console.

---

## Action reference

| Action | Approval | Notes |
|---|---|---|
| `trigger_scan` | no | starts a job; level 1 of the pipeline |
| `list_seeds` | no | |
| `expand_seeds` | no | starts a job; level 2 of the pipeline |
| `list_opportunities` | no | |
| `delete_opportunities` | yes | prune before generating |
| `generate_content` | yes | starts a job; writes drafts |
| `list_drafts` | no | unpublished by default |
| `publish_content` | yes | starts a job; publishes to the product's configured destination(s) |
| `index_content` | yes | starts a job; GSC sitemap submit |
| `get_job_status` | no | poll any job_id returned above |
| `list_jobs` | no | recent jobs, optionally filtered by status |
