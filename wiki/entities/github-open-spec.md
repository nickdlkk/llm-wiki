---
title: OpenSpec
type: entity
source: github:Fission-AI/OpenSpec
created: 2026-04-17
updated: 2026-04-17
tags:
  - spec-driven-development
  - sdd
  - typescript
  - claude-code
  - delta-format
---

# OpenSpec

*"Iterative Spec-Driven Development — not waterfall."*

**Repo:** `Fission-AI/OpenSpec` | **Lang:** TypeScript | **License:** MIT | **Status:** Active

---

## Summary

OpenSpec is a lightweight spec-driven development framework with a unique **delta format** for spec changes. Instead of rewriting full specs, changes are stored as targeted diffs (`ADDED`/`MODIFIED`/`REMOVED`/`RENAMED Requirements`) that merge into main specs.

**Key differentiator:** Delta specs eliminate spec drift — only the *change* is stored, not a duplicate of the entire spec.

---

## Architecture

- **Profile system** (`core`/`custom`) — controls which conventions activate
- **Delivery system** (`both`/`skills`/`commands`) — controls how specs are exposed to AI tools
- **CommandAdapterRegistry** — 26 AI tool adapters (Claude, Cursor, Windsurf, Gemini CLI, Codex, etc.)
- **Artifact-driven workflows** — `/opsx:propose` creates proposal + design + specs + tasks in one step

---

## vs Spec-Kit

| Aspect | OpenSpec | Spec-Kit |
|--------|----------|----------|
| Format | Markdown + delta diffs | Markdown + frontmatter |
| Workflow | Opinionated conventions | Programmable YAML |
| Tool support | 26 adapters | 22 integrations |
| Complexity | Lightweight | Heavier |
| Best for | Individuals, fast iteration | Teams with specific process needs |

---

## Strengths

- Delta format eliminates spec drift
- Excellent multi-tool support (26 adapters)
- Low ceremony, works immediately
- Skills + commands dual delivery
- Active development

## Weaknesses

- Fixed workflow templates
- Less programmable than Spec-Kit
- Delta format not formally standardized
