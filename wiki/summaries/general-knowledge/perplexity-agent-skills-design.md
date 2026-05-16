---
title: "Perplexity Agent Skills Design"
type: summary
created: 2026-05-12
updated: 2026-05-12
sources:
  - https://research.perplexity.ai/articles/designing-refining-and-maintaining-agent-skills-at-perplexity
tags: [agent-skills, skill-engineering, perplexity]
---

# Perplexity Agent Skills Design

## Core Thesis

Skill writing and code writing are fundamentally different disciplines. A Skill is **context for models**, not software to execute. Many patterns that make good code make bad Skills.

## Key Insights

### Zen of Skills vs Zen of Python

Writing good Python (explicit, simple, sparse) often produces **worse** Skills. The right principles for Skills: complexity is a feature, activation is implicit, every token costs, gotchas are highest-value content, and if it's easy to explain the model already knows it.

### Three-Tier Progressive Loading

| Tier | Cost | Trigger |
|------|------|---------|
| Index | ~100 tokens/Skill | Always (every session) |
| Load | ~5,000 tokens | When loaded |
| Runtime | Unbounded | Only when read |

This means: descriptions (index) must be extremely terse and high-signal. Loaded body should be ≤5,000 tokens. Runtime files can be arbitrarily large.

### Description is the Hard Part

Description = routing trigger ("Load when..."), NOT documentation. Must be written from user's frustrated intent, not from the Skill's feature list.

### Gotchas are the Content

The most valuable thing in a Skill is the gotcha section. Model fails → add a gotcha. Skills are append-only; the gotcha list grows organically with production failures.

### Self-Generated Skills Don't Work

Research: LLMs cannot reliably author the procedural knowledge they benefit from consuming. You cannot prompt-engineer your way to a good Skill.

## Action Items

- Write evals BEFORE writing the Skill
- Keep descriptions ≤50 words, start with "Load when..."
- Every sentence: "Would the agent get this wrong without this?"
- Gotchas grow over time; don't over-engineer on day one
- Submit single changeset + eval set together
- Test across multiple model families (behavior differs)
