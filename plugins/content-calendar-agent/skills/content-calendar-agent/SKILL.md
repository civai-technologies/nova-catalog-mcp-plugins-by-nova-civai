---
name: content_calendar_agent
description: Nova content-calendar-agent MCP — read before preview_document / tools/call.
---

# Content Calendar Agent MCP — assistant guide

Read this resource **before** calling tools on **Content Calendar Agent** (`https://nova.civai.co/mcp/agents/content-calendar-agent`).

## Auth

| Item | Detail |
|------|--------|
| **Auth** | Nova API key — `Authorization: Bearer <token>` or `X-API-Key` |
| **Setup** | https://nova.civai.co/api-keys |
| **Credits** | Each successful MCP `tools/call` debits catalog MCP credits (failed calls are not charged). |

Tool names are namespaced: `bgpt_pdf_generator__*`, `slidegen__*`.

---

## What this MCP is (and is not)

This endpoint exposes **PDF Generator MCP** + **Slidegen** tools for the catalog **Content Calendar Agent**. It does **not** expose a separate “run the Nova agent chat” tool.

| Surface | Who writes the calendar |
|---------|-------------------------|
| **Nova web chat** | https://nova.civai.co/agent/content-calendar-agent — full agent loop on Nova |
| **MCP (Cursor, Claude Desktop, …)** | The **host AI assistant** acts as Content Calendar Agent: read this file, interpret the user’s brief, then call `preview_document` with the full markdown table. Do **not** bypass MCP with Python/curl scripts. Do **not** only paste a calendar in chat without calling `preview_document`. |

---

## Mandatory workflow (MCP hosts)

```
User objective (channels, weeks, brand, pillars, CTAs)
        │
        ▼
  You (assistant) = Content Calendar Agent persona
        │
        ▼
  bgpt_pdf_generator__preview_document
    { title, content: "<markdown tables>" }
        │
        ▼
  Show draft (get_preview / tool result) → STOP
        │
        ▼
  Ask user Yes/No for downloadable PDF
        │
        ▼ (only after explicit Yes)
  bgpt_pdf_generator__generate_pdf { preview_id }
```

**Hard rules**

1. **Never** call `generate_pdf` in the same turn as `preview_document`.
2. **Never** call `generate_pdf` with `{}` — always pass `preview_id` from the draft.
3. User says “PDF” in the first message → still **preview → show → Yes/No → PDF**.
4. Do **not** treat “non-interactive MCP” as permission to skip Yes/No with the user.
5. Return PDF Generator tool output **as-is** — do not invent a second PDF path or rewrite into a meta status card.
6. **Never** invent more rows or weeks than the user asked for.
7. **Slidegen** is for graphics only; after a graphic URL exists, update the draft with `preview_document` (`update_preview_id`).

---

## Canonical calendar columns

Every calendar table must use these columns (markdown):

`Day | Content Pillar | Post Type | Creative/Visual Direction | Creative Copy | Caption | CTA | Hashtags | Basic graphics`

- **Post Type** — channel or format (e.g. LinkedIn, X, Instagram, carousel, thread).
- **Basic graphics** — leave empty unless the user supplied a URL or Slidegen produced a link you are adding to that row.

Use `---` between PDF pages when the draft spans multiple weeks (each page should have a heading).

---

## Tool reference

### PDF Generator (`bgpt_pdf_generator__*`)

| Tool | When to use |
|------|-------------|
| `preview_document` | Create or revise draft markdown in session storage. Required args: `content`; optional `title`, `update_preview_id`. **Agent-only** — this is how calendars are stored. |
| `get_preview` | Show an existing draft body to the user (`preview_id` optional if only one draft). |
| `list_previews` | List draft titles and ids in this MCP session. |
| `generate_pdf` | **Only after user Yes.** Pass `preview_id`. |
| `delete_preview` | User asked to discard a draft (`preview_id` required). |

Hidden from suggestion chips by design — hosts still call them per this guide.

### Slidegen (`slidegen__*`)

| Tool | When to use |
|------|-------------|
| `generate_single_graphic` / `generate_image` / `generate_multiple_graphics` | User asked for visuals for specific calendar rows. |
| `converse` | Ambiguous graphic request only — not a substitute for calendar creation. |

After Slidegen returns an image URL, patch the calendar via `preview_document` with `update_preview_id`.

---

## Example user brief → what you do

**User:** “4-week October 2026 social calendar for Nova platform: LinkedIn 3/wk, X 4/wk, IG 2/wk. Pillars: platform shift, specialists, custom agents, MCP builders, proof, Explore. CTAs to /platform, /explore, /api-keys. No fake metrics. Preview only until I say Yes for PDF.”

**You:**

1. Confirm scope (dates, cadence, pillars) in one short sentence if anything is missing.
2. Invent on-brand rows with the canonical columns (counts must match requested cadence).
3. Call `bgpt_pdf_generator__preview_document` with a real title and full markdown (not “here is a preview” placeholder text).
4. Present the draft from the tool result / `get_preview`.
5. Ask: “Generate downloadable PDF? (Yes/No)”
6. Call `generate_pdf` only after **Yes**.

---

## Markdown mockup (draft shape)

```markdown
# Acme — October 2026 content calendar

| Day | Content Pillar | Post Type | Creative/Visual Direction | Creative Copy | Caption | CTA | Hashtags | Basic graphics |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Oct 1 | Platform shift | LinkedIn | … | … | … | https://example.com/platform | #… | |

---

## Week 2

| Day | Content Pillar | … |
| --- | --- | --- |
```

---

## Guardrails

1. **No fabricated traction** — do not invent MRR, customer counts, or retention unless the user provided them.
2. **Public branding** — “Nova by CIVAI” / catalog agent names; do not leak internal codenames in customer-facing copy.
3. **Do not** send agent `instructions`/`context` patterns into social captions (this agent creates marketing copy, not CRM playbooks).
4. Prefer full URLs in **CTA** when the user names Nova paths (`https://nova.civai.co/explore`, etc.).

---

## Related docs (API repo)

| Path | Topic |
|------|--------|
| `docs/external_mcp_nova_flow.md` | Content Calendar + PDF Generator flow mockup |
| `utils/nova_agents.json` | `content-calendar-agent` system prompt (source of truth with this file) |
| `constraints.md` | PDF Generator + Content Calendar constraints |

---

## Resource URI

This file is exposed as an MCP resource:

- **URI:** `civai-nova://content-calendar-agent/SKILLS.md`

On connect: call `resources/list`, then `resources/read` with that URI (API key required). Host assistants should load this **before** the first `tools/call`.
