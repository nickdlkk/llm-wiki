# Agent Skills

**Production-grade engineering skills for AI coding agents.**

Skills encode the workflows, quality gates, and best practices that senior engineers use when building software. These ones are packaged so AI agents follow them consistently across every phase of development.

```
  DEFINE          PLAN           BUILD          VERIFY         REVIEW          SHIP
 ┌──────┐      ┌──────┐      ┌──────┐      ┌──────┐      ┌──────┐      ┌──────┐
 │ Idea │ ───▶ │ Spec │ ───▶ │ Code │ ───▶ │ Test │ ───▶ │  QA  │ ───▶ │  Go  │
 │Refine│      │  PRD │      │ Impl │      │Debug │      │ Gate │      │ Live │
 └──────┘      └──────┘      └──────┘      └──────┘      └──────┘      └──────┘
  /spec          /plan          /build        /test         /review       /ship
```

## Commands

7 slash commands that map to the development lifecycle. Each one activates the right skills automatically.

| What you're doing | Command | Key principle |
|-------------------|---------|---------------|
| Define what to build | `/spec` | Spec before code |
| Plan how to build it | `/plan` | Small, atomic tasks |
| Build incrementally | `/build` | One slice at a time |
| Prove it works | `/test` | Tests are proof |
| Review before merge | `/review` | Improve code health |
| Simplify the code | `/code-simplify` | Clarity over cleverness |
| Ship to production | `/ship` | Faster is safer |

Skills also activate automatically based on what you're doing — designing an API triggers `api-and-interface-design`, building UI triggers `frontend-ui-engineering`, and so on.

## All 20 Skills

### Define - Clarify what to build

| Skill | What It Does | Use When |
|-------|-------------|----------|
| idea-refine | Structured divergent/convergent thinking to turn vague ideas into concrete proposals | You have a rough concept that needs exploration |
| spec-driven-development | Write a PRD covering objectives, commands, structure, code style, testing, and boundaries before any code | Starting a new project, feature, or significant change |

### Plan - Break it down

| Skill | What It Does | Use When |
|-------|-------------|----------|
| planning-and-task-breakdown | Decompose specs into small, verifiable tasks with acceptance criteria and dependency ordering | You have a spec and need implementable units |

### Build - Write the code

| Skill | What It Does | Use When |
|-------|-------------|----------|
| incremental-implementation | Thin vertical slices - implement, test, verify, commit. Feature flags, safe defaults, rollback-friendly changes | Any change touching more than one file |
| test-driven-development | Red-Green-Refactor, test pyramid (80/15/5), test sizes, DAMP over DRY, Beyonce Rule, browser testing | Implementing logic, fixing bugs, or changing behavior |
| context-engineering | Feed agents the right information at the right time - rules files, context packing, MCP integrations | Starting a session, switching tasks, or when output quality drops |
| source-driven-development | Ground every framework decision in official documentation - verify, cite sources, flag what's unverified | You want authoritative, source-cited code for any framework or library |
| frontend-ui-engineering | Component architecture, design systems, state management, responsive design, WCAG 2.1 AA accessibility | Building or modifying user-facing interfaces |
| api-and-interface-design | Contract-first design, Hyrum's Law, One-Version Rule, error semantics, boundary validation | Designing APIs, module boundaries, or public interfaces |

### Verify - Prove it works

| Skill | What It Does | Use When |
|-------|-------------|----------|
| browser-testing-with-devtools | Chrome DevTools MCP for live runtime data - DOM inspection, console logs, network traces, performance profiling | Building or debugging anything that runs in a browser |
| debugging-and-error-recovery | Five-step triage: reproduce, localize, reduce, fix, guard. Stop-the-line rule, safe fallbacks | Tests fail, builds break, or behavior is unexpected |

### Review - Quality gates before merge

| Skill | What It Does | Use When |
|-------|-------------|----------|
| code-review-and-quality | Five-axis review, change sizing (~100 lines), severity labels (Nit/Optional/FYI), review speed norms, splitting strategies | Before merging any change |
| code-simplification | Chesterton's Fence, Rule of 500, reduce complexity while preserving exact behavior | Code works but is harder to read or maintain than it should be |
| security-and-hardening | OWASP Top 10 prevention, auth patterns, secrets management, dependency auditing, three-tier boundary system | Handling user input, auth, data storage, or external integrations |
| performance-optimization | Measure-first approach - Core Web Vitals targets, profiling workflows, bundle analysis, anti-pattern detection | Performance requirements exist or you suspect regressions |

### Ship - Deploy with confidence

| Skill | What It Does | Use When |
|-------|-------------|----------|
| git-workflow-and-versioning | Trunk-based development, atomic commits, change sizing (~100 lines), the commit-as-save-point pattern | Making any code change (always) |
| ci-cd-and-automation | Shift Left, Faster is Safer, feature flags, quality gate pipelines, failure feedback loops | Setting up or modifying build and deploy pipelines |
| deprecation-and-migration | Code-as-liability mindset, compulsory vs advisory deprecation, migration patterns, zombie code removal | Removing old systems, migrating users, or sunsetting features |
| documentation-and-adrs | Architecture Decision Records, API docs, inline documentation standards - document the *why* | Making architectural decisions, changing APIs, or shipping features |
| shipping-and-launch | Pre-launch checklists, feature flag lifecycle, staged rollouts, rollback procedures, monitoring setup | Preparing to deploy to production |

## Agent Personas

Pre-configured specialist personas for targeted reviews:

| Agent | Role | Perspective |
|-------|------|-------------|
| code-reviewer | Senior Staff Engineer | Five-axis code review with "would a staff engineer approve this?" standard |
| test-engineer | QA Specialist | Test strategy, coverage analysis, and the Prove-It pattern |
| security-auditor | Security Engineer | Vulnerability detection, threat modeling, OWASP assessment |

## Reference Checklists

| Reference | Covers |
|-----------|--------|
| testing-patterns.md | Test structure, naming, mocking, React/API/E2E examples, anti-patterns |
| security-checklist.md | Pre-commit checks, auth, input validation, headers, CORS, OWASP Top 10 |
| performance-checklist.md | Core Web Vitals targets, frontend/backend checklists, measurement commands |
| accessibility-checklist.md | Keyboard nav, screen readers, visual design, ARIA, testing tools |

## How Skills Work

Every skill follows a consistent anatomy:

- **Frontmatter** — name, description, trigger conditions
- **Overview** — What this skill does
- **When to Use** — Triggering conditions
- **Process** — Step-by-step workflow
- **Rationalizations** — Excuses + rebuttals (anti-rationalization tables)
- **Red Flags** — Signs something's wrong
- **Verification** — Evidence requirements

Key design choices:
- **Process, not prose.** Skills are workflows agents follow, not reference docs they read.
- **Anti-rationalization.** Every skill includes a table of common excuses agents use to skip steps with documented counter-arguments.
- **Verification is non-negotiable.** Every skill ends with evidence requirements - tests passing, build output, runtime data.
- **Progressive disclosure.** The `SKILL.md` is the entry point. Supporting references load only when needed.

## Why Agent Skills?

AI coding agents default to the shortest path - which often means skipping specs, tests, security reviews, and the practices that make software reliable. Skills bake in best practices from Google's engineering culture — including concepts from Software Engineering at Google and Google's engineering practices guide.

Key principles embedded:
- Hyrum's Law in API design
- Beyonce Rule and test pyramid in testing
- Change sizing and review speed norms in code review
- Chesterton's Fence in simplification
- Trunk-based development in git workflow
- Shift Left and feature flags in CI/CD
- Code-as-liability in deprecation

## Project Structure

```
agent-skills/
├── skills/                    # 20 core skills (SKILL.md per directory)
├── agents/                    # 3 specialist personas
├── references/                # 4 supplementary checklists
├── hooks/                     # Session lifecycle hooks
├── .claude/commands/          # 7 slash commands (Claude Code)
├── .gemini/commands/          # 7 slash commands (Gemini CLI)
└── docs/                      # Setup guides per tool
```

## License

MIT
