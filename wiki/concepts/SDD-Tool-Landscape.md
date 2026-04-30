---
title: SDD Tool Landscape
type: concept
created: 2026-04-17
updated: 2026-04-17
sources:
  - github:Fission-AI/OpenSpec
  - github:github/spec-kit
  - github:potpie-ai/potpie
  - github:gsd-build/get-shit-done
  - github:sighup/claude-workflow
  - github:spec2ship/spec2ship
  - github:deepeshBodh/human-in-loop
  - github:fntune/swarm
  - github:mindfold-ai/Trellis
  - github:SYZ-Coder/superpowers-openspec-team-skills
  - github:zxzvsdcj/spec-first-superpowers
tags:
  - spec-driven-development
  - sdd
  - ai-coding-agents
  - tool-landscape
---

# SDD Tool Landscape

Survey of Spec-Driven Development tools in the AI coding agent ecosystem (2026). These tools add a specification layer between human intent and code generation — reducing hallucination, maintaining consistency, and enabling traceable development. (14 tools surveyed)

## Comparison Matrix

| Tool | Focus | Language | Workflow Model | Multi-Agent | Key Innovation |
|------|-------|----------|----------------|-------------|----------------|
| **OpenSpec** | Spec + delta merge | TypeScript | Artifact-driven templates | 26 tool adapters | Delta-formatted spec changes |
| **Spec-Kit** | Workflow + integrations | Python | YAML programmable pipeline | 22 integrations | Programmable workflow engine |
| **Get Shit Done** | Context hygiene + meta-prompting | TypeScript | Skill system | Multi-agent | Context rot prevention |
| **Potpie** | Knowledge graph from codebase | Python | Agent orchestration | Yes (graph-based) | Index-first, not spec-first |
| **GenericAgent** | Self-evolution via skill crystallization | Python | 9 atomic tools + ~100-line loop | No | Real browser, self-bootstrap, <30K context |
| **Claude-Workflow** | Spec → validated implementation | Python | Dependency-aware task graph | Parallel subagents | Complete lifecycle |
| **HumanInLoop** | Spec enforcement + human checkpoints | Python | Deterministic DAG | MCP-based | Human-in-the-loop enforcement |
| **Spec2Ship** | Multi-role deliberation | TypeScript | Roundtable agent discussion | 12 specialized roles | Adversarial spec review |
| **Swarm** | Parallel worktree orchestration | Python | YAML plan + git worktrees | Yes (worktree-isolated) | Crash recovery via SQLite |
| **Vibe-Skills** | 340+ skills + governed runtime | Python | VCO 6-stage state machine | Yes (Root/Child lanes) | 129 governance rules, intelligent routing |
| **Trellis** | Spec injection + task workflow | Node.js + Python | Slash commands + git worktree | Yes (14 platforms) | Multi-platform spec injection, task continuity |
| **superpowers-openspec-team-skills** | Superpowers + OpenSpec for teams | Multi (shell/ps1) | Opt-in explicit workflows | Yes (Codex/Cursor/Claude) | Tool-specific bundles + project memory + cross-session .superpowers-memory/ |
| **spec-first-superpowers** | Spec-before-code for Cursor | Shell | /super-spec G0-G4 quality gates | Subagent-based | Inline self-review (~30s) + MemPalace cross-session memory + complexity triage |

## Key Patterns

### Spec Format

| Tool | Format | Delta? |
|------|--------|--------|
| OpenSpec | Markdown (SHALL/MUST, scenarios required) | Yes — ADDED/MODIFIED/REMOVED |
| Spec-Kit | Markdown + frontmatter | No — full spec per change |
| GSD | Meta-prompting conventions | Implicit |
| Claude-Workflow | Structured spec + Gherkin | Partial |
| HumanInLoop | constitution.md + spec.md + plan | Tasks as DAG |
| Spec2Ship | Multi-role deliberation | Collaborative draft |

### Multi-Agent Patterns

1. **Parallel subagents** (claude-workflow, swarm) — dependency-graph-based parallel execution
2. **Role-based deliberation** (Spec2Ship) — multiple agents represent stakeholders
3. **Graph-based reasoning** (Potpie) — agents query knowledge graph
4. **Tool adapter registry** (OpenSpec, Spec-Kit) — unified spec format across many AI tools
5. **Worktree isolation** (swarm) — git worktrees prevent parallel agent file conflicts

## Research Gaps

- No standard spec interchange format between tools
- Delta spec format (OpenSpec) has no formal schema beyond Markdown conventions
- Human-in-the-loop enforcement varies widely
- Spec versioning/branching not well addressed
- Integration with formal verification (TLA+) is absent
