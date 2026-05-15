---
name: scrape
description: Pull web page data (first=prototype, subsequent=permanent skill)
icon: 🌐
---

# scrape

Pull data from a web page. First call on a new intent prototypes the flow via $B primitives and returns JSON. Subsequent calls on a matching intent route to a codified browser-skill and return in ~200ms.

## When to Use

Read-only — for mutating flows (form fills, clicks, submissions), use /automate.

Use when asked to "scrape", "get data from", "pull", "extract from", or "what's on" a page.

## Triggers

- scrape this page
- get data from
- pull from
- extract from
- what is on

## See Also

- [[skillify]] — Codify scrape flow into permanent skill
- [[investigate]] — Investigate scraped data
- [[qa]] — Verify scraped data quality

## Category

[[entities/concepts-frameworks/gstack]]
