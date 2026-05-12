---
title: "Perplexity Agent Skills"
type: entity
created: 2026-05-12
updated: 2026-05-12
sources:
  - https://research.perplexity.ai/articles/designing-refining-and-maintaining-agent-skills-at-perplexity
  - https://agentskills.io/home
tags: [agent-skills, perplexity, skill-engineering]
---

# Perplexity Agent Skills

Public research guide + internal engineering framework from Perplexity's Agents team for designing, refining, and maintaining Agent Skills.

## What It Covers

- How Skills differ from code (different intuitions, different antipatterns)
- The four dimensions of a Skill: directory, format, invocable, progressive
- Three-tier token budget: Index / Load / Runtime
- Step-by-step Skill construction: write evals first, description is hardest part
- The Gotchas Flywheel: append-only maintenance model
- Multi-level hierarchy for complex domains (e.g., 1,945 IRS code sections → 20 areas → 3 levels)
- Cross-model eval requirements (GPT vs Claude Opus vs Claude Sonnet behave differently)

## Key Claims

- LLMs cannot self-generate useful Skills for themselves (research-backed)
- Every Skill is a tax on every other Skill (action at a distance)
- Gotchas > instructions for model guidance
- Description is a routing trigger, not documentation

## Related

- [[agent-skills]] — Addy Osmani's 20 engineering skills library (different scope: general agent capabilities)
- [[gstack]] — Garry Tan's Claude Code skill packs (production harness engineering reference)
- [[learn-harness-engineering]] — Courses on harness engineering methodology
