---
title: "gstack — Full Skill Reference"
source_url: https://github.com/garrytan/gstack
source_type: documentation
date: 2026-04-21
ingested: 2026-04-21
tags: [claude-code, workflow, harness-engineering, yc]
---

# gstack — Full Skill Reference

**Source**: [garrytan/gstack](https://github.com/garrytan/gstack) · GitHub · 2026

## Key takeaways

- gstack transforms Claude Code from a single AI assistant into a **multi-role virtual engineering team** (CEO, EM, Staff Eng, QA, SRE, Release Eng)
- The core insight is **persistent browser daemon** — sub-second latency, cookies/tabs carry over between commands
- All skills are Markdown SKILL.md files with bash preamble + allowed-tools + structured workflow + telemetry logging
- Skills auto-log learnings to `~/.gstack/projects/<slug>/learnings.jsonl` for cross-session memory
- `/learn` skill manages the learnings — search, prune, export by type (pattern/pitfall/preference/architecture)

## Skill roster

### Product & Planning
| Skill | Role | Core function |
|-------|------|--------------|
| `/office-hours` | YC Office Hours | 6 forcing questions, reframes product before writing code |
| `/plan-ceo-review` | CEO/Founder | Find the 10-star product; 4 modes: Expansion/SelExp/Hold/Reduction |
| `/plan-eng-review` | Eng Manager | Lock architecture, data flow, diagrams, edge cases, tests |
| `/plan-design-review` | Senior Designer | Plan-mode design review, 0-10 ratings, 7 design passes |
| `/design-consultation` | Design Partner | Build design system from scratch with creative risks |
| `/design-review` | Designer Who Codes | Live-site 80-item visual audit + fix loop |
| `/design-shotgun` | Design Explorer | Multiple AI variants, comparison board, iterate to approval |
| `/design-html` | Design Engineer | Production-quality Pretext HTML from approved mockups |
| `/autoplan` | Review Pipeline | CEO → Design → Eng review chain, auto-resolves decisions |

### Execution & Verification
| Skill | Role | Core function |
|-------|------|--------------|
| `/review` | Staff Engineer | Structural audit: N+1, race conditions, trust boundaries, completeness gaps |
| `/investigate` | Debugger | Root-cause debugging, Iron Law: no fix without investigation |
| `/qa` | QA Lead | Playwright-based testing, diff-aware, auto regression tests |
| `/qa-only` | QA Reporter | Same methodology, report only |
| `/browse` | QA Engineer | Persistent Chromium daemon, ~100ms/command, real browser state |
| `/setup-browser-cookies` | Session Manager | Import cookies from real browser (Chrome/Arc/Brave/Edge) |
| `/canary` | SRE | Post-deploy monitoring loop, console errors, perf regressions |
| `/benchmark` | Performance Engineer | Core Web Vitals, real Chromium measurements, before/after diff |

### Release & Operations
| Skill | Role | Core function |
|-------|------|--------------|
| `/ship` | Release Engineer | Sync → test → coverage audit → push → PR |
| `/land-and-deploy` | Release Engineer | Merge → CI → deploy → canary verify, one command |
| `/setup-deploy` | Deploy Configurator | Detect platform (Fly/Render/Vercel/Netlify/Heroku/GHA), write to CLAUDE.md |
| `/cso` | Chief Security Officer | OWASP Top 10 + STRIDE threat model |
| `/document-release` | Technical Writer | Sync docs with diff, catch stale READMEs |
| `/retro` | Eng Manager | Weekly retro: commit history, shipping velocity, test health trends |

### Memory & Learning
| Skill | Role | Core function |
|-------|------|--------------|
| `/learn` | Memory | Manage learnings.jsonl — search, prune, export, stats |

### Multi-AI
| Skill | Role | Core function |
|-------|------|--------------|
| `/codex` | Second Opinion | OpenAI Codex CLI review, 3 modes: review/challenge/consult |

### Safety
| Skill | Role | Core function |
|-------|------|--------------|
| `/careful` | Safety Guardrails | Destructive command warnings (rm -rf, DROP TABLE, force-push) |
| `/freeze` | Edit Lock | Restrict edits to one directory |
| `/guard` | Full Safety | /careful + /freeze combined |
| `/unfreeze` | Unlock | Remove freeze boundary |
| `/open-gstack-browser` | Co-presence | Headed Chromium with sidebar, watch agent live |
| `/gstack-upgrade` | Self-Updater | Upgrade global + vendored installs, show changelog |

## Learnings data model

```json
{"skill":"review","type":"pattern","key":"nextjs-api-route-error","insight":"...","confidence":8,"source":"observed","files":["app/api/data/route.ts"],"ts":"2026-04-20T10:23:00Z"}
```

Types: `operational` | `pattern` | `pitfall` | `preference` | `architecture`
Source: `observed` | `user-stated`

## Architecture highlights

- **Daemon model**: Chromium runs as long-lived process; CLI talks HTTP to Bun server on localhost
- **Bun chosen for**: compiled binary (~58MB, no node_modules), native SQLite (cookie decryption), native HTTP server
- **State file**: `.gstack/browse.json` (pid, port, token, version) — atomic write, mode 0600
- **Bearer token auth**: random UUID per session, 401 on mismatch
- **Version auto-restart**: binary version checked against running server, kills + restarts on mismatch
- **Ref system**: ARIA tree → sequential @e1/@e2 refs → Playwright Locators (no DOM mutation, CSP-safe)
- **Prompt injection defense**: 6-layer system — L1 content security, L4 ML classifier (22MB BERT-small ONNX), L5 canary token, L6 ensemble combiner

## Usage stats (as of Apr 2026)

- 75k+ GitHub stars
- 14,965 unique installations (opt-in telemetry)
- 305,309 total skill invocations
- ~7,000 weekly active users
- 95.2% success rate across all runs
- Top skills: /qa (57,650), /plan-eng-review (28,014), /office-hours (24,817), /ship (18,899)
