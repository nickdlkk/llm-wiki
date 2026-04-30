---
title: "On the LOC controversy"
source_url: https://github.com/garrytan/gstack/blob/main/docs/ON_THE_LOC_CONTROVERSY.md
source_type: article
date: 2026-04-18
ingested: 2026-04-21
tags: [claude-code, productivity, ai-engineering, gstack]
---

# On the LOC controversy

**Source**: Garry Tan · GitHub · April 18, 2026

## Key takeaways

- Raw LOC is a garbage metric — 40 years of known. But if AI inflates LOC by some factor, compute the **deflation** and report the deflated number
- After applying 2x AI-verbosity deflation, still **408x** more productive than 2013 part-time baseline
- **Revert rate 2.0%** (7/351 commits) — within normal OSS band (1-3%)
- **Test coverage** went from ~100 to 2,000+ tests — the real unlock for AI-assisted coding
- 75k+ GitHub stars, 14,965 installations, 305k skill invocations, 95.2% success rate

## Core claims

Garry Tan posted 600K lines of production code in 60 days. Critics said "that's just AI slop." His response: compute the honest deflated number.

**Raw comparison (2013 vs 2026, 108 days):**
| Metric | 2013 | 2026 | Multiple |
|--------|-----:|-----:|---------:|
| Logical SLOC | 5,143 | 1,233,062 | 240x |
| SLOC/day | 14 | 11,417 | 810x |
| Commits | 71 | 351 | 4.9x |
| Files touched | 290 | 13,629 | 47x |

**With 2x AI-verbosity deflation**: 5,708 SLOC/day → **408x** on 2013 baseline.

**With 10x deflation (skeptic's upper bound)**: 81x — still enormous.

## Testing as the real unlock

> "Testing at multiple levels is what makes AI-assisted coding actually work. Unit tests, E2E tests, LLM-as-judge evals, smoke tests, slop scans. Without those layers, you're just generating confident garbage at high speed."

Key data:
- Tests: 100 → 2,000+ across repos
- gstack scored 5.24 on slop-scan (worst measured at time), cut 62% in one session
- `/plan-tune` had scope **ROLLBACK** from CEO plan because Codex outside-voice review surfaced 15+ findings that 4 Claude reviews missed

## Quality metrics

- **Revert rate**: 2.0% (7/351) — normal OSS band is 1-3%
- **Post-merge fixes**: 6.3% (22/351) — healthy fix cycle
- **Greptile integration**: catches real bugs on PRs; gstack triages findings (valid → fix, already-fixed → acknowledge, false positive → push back)

## Concessions

- 2026 numbers are **greenfield-dominated** — maintenance on large legacy codebases would show lower multiplier
- "Shipped" means different things across eras — some 2026 products may be dead in 2 years
- Quality-adjusted productivity not fully proven (no clean bug-density comparison)
- Time-to-first-user is the **right metric**, not LOC — but LOC is downstream evidence of the same shift

## Notable quotes

> "The delta isn't that I became a better programmer. If anything, my mental model of coding has atrophied. The delta is that AI let me actually ship the things I always wanted to build."

> "The gap between 'I want this tool' and 'this tool exists and I'm using it' collapsed from 3 weeks to 3 hours."
