---
title: Spec2Ship
type: entity
source: github:spec2ship/spec2ship
created: 2026-04-17
updated: 2026-04-17
tags:
  - claude-code
  - multi-agent
  - deliberation
  - spec-driven
  - roundtable
---

# Spec2Ship

*"Spec-driven development framework that orchestrates multi-agent workflows on top of Claude Code."*

**Repo:** `spec2ship/spec2ship` | **Lang:** TypeScript | **License:** Apache 2.0 | **Status:** Active

---

## Summary

Spec2Ship introduces a **roundtable discussion model** — instead of a human writing a spec and an AI implementing it, 12 specialized agents (each representing a real role) debate and refine requirements collaboratively before code is written.

**Philosophy:** Specs are *executable intent*. Not a prompt library — a deliberation system with configurable facilitation.

---

## Multi-Agent Roundtable

12 specialized roles that can participate in spec deliberation:
- Product Manager
- Security Engineer
- Backend Developer
- Frontend Developer
- DevOps Engineer
- QA Engineer
- Performance Engineer
- Data Engineer
- UX Designer
- Legal/Compliance
- Business Analyst
- Technical Lead (facilitator)

---

## vs Other Tools

| Aspect | OpenSpec | Spec2Ship |
|--------|----------|-----------|
| Spec creation | Human-AI alignment | AI-AI deliberation |
| Roles | Single agent | 12 specialized agents |
| Approval | Implicit | Explicit human approval |
| Scope | Individual | Team/org simulation |

---

## Strengths

- Adversarial review built into spec creation
- Multi-perspective spec refinement
- Simulates team deliberation at scale
- Human approval gate before implementation

## Weaknesses

- Requires Claude Code
- Complex setup for simple use cases
- Deliberation overhead for small features
