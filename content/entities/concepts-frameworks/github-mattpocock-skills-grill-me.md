---
title: grill-me
type: entity
created: 2026-04-29
updated: 2026-04-29
sources:
  - https://github.com/mattpocock/skills/blob/3e3ca9b9fa007614c65fab70469127b25841881d/skills/grill-me/SKILL.md
tags:
  - skill
  - interview
  - questioning
  - understanding
---

# grill-me

A skill that implements **relentless Socratic questioning** to stress-test plans and designs until shared understanding is reached.

## Origin

Part of [mattpocock/skills](https://github.com/mattpocock/skills), a personal skill library for AI coding agents.

## Core Mechanism

The skill operates as a **decision tree walker** — it recursively drills down each branch of a plan/design, resolving dependencies between decisions one-by-one.

### Key Behaviors

1. **One question at a time** — avoids overwhelming the user, forces sequential reasoning
2. **Provides recommended answers** — for each question, the skill states its recommended answer (not just asks)
3. **Codebase exploration fallback** — if a question can be answered by exploring the codebase, it does so instead of asking

### Trigger Condition

> User wants to stress-test a plan, get grilled on their design, or explicitly says "grill me"

## Relationship to Harness Engineering

This skill embodies the [[Agent Review Pattern]] — instead of accepting a plan at face value, it forces explicit reasoning about each decision branch. The "recommended answer" behavior mirrors the Reviewer Agent providing a concrete alternative.

However, grill-me is **user-driven** (the user initiates) while the Agent Review Pattern is **system-initiated** (built into the workflow).

## Skill Text

```yaml
name: grill-me
description: >
  Interview the user relentlessly about a plan or design until reaching
  shared understanding, resolving each branch of the decision tree.
  Use when user wants to stress-test a plan, get grilled on their design,
  or mentions "grill me".

instructions: |
  Interview me relentlessly about every aspect of this plan until we reach
  a shared understanding. Walk down each branch of the design tree,
  resolving dependencies between decisions one-by-one. For each question,
  provide your recommended answer.

  Ask the questions one at a time.

  If a question can be answered by exploring the codebase, explore the
  codebase instead.
```

## Observations

- **Minimalist design**: 3 sentences, no boilerplate, direct trigger-based routing
- **No mention of stopping conditions**: assumes the interviewer (AI) knows when shared understanding is reached
- **Codebase-first fallback**: distinguishes between "ask the user" vs "answer yourself via exploration" — reduces unnecessary back-and-forth
