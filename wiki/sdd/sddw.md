---
title: sddw
type: entity
created: 2026-05-24
updated: 2026-05-24
sources:
  - https://github.com/sermakarevich/sddw
tags:
  - spec-driven-development
  - sdd
  - claude-code
  - ai-coding-agents
  - workflow
---

# sddw

Spec-Driven Development Workflow for Claude Code — a 7-step structured workflow that replaces vague prompts with collaborative specification authoring before code generation. MIT license, Shell + Markdown, no runtime dependencies.

## Core Philosophy

Standard AI coding agent usage: describe → get code → fix → repeat. Works for small tasks, breaks for anything non-trivial — context lost between sessions, architectural decisions buried in chat history, no reviewable artifact before code is written.

sddw inverts this: **collaborate on specs first, generate code mechanically second**. Specs become the primary artifact: peer-reviewable, version-controlled, persistent. Code generation is guided by approved specs, not pulled from a vague prompt.

Research backing: detailed specs reduce AI code errors by 50%, security defects by 73%, architecture-misaligned PRs by 60%.

## 7-Step Workflow

```
Requirements → Code Analysis (optional) → Design → Taskify → Implement → Verify → Self-Improve
```

### Step 1: Requirements

`/sddw:requirements <feature> [--auto]`

Collaboratively produce `requirements.md` through guided dialog:

- **Discover** — understand feature through one-at-a-time questions, challenge vagueness
- **Research & Propose** — research SOTA, propose 2-3 ranked options per section
- **Confirm & Generate** — user approves each block before spec is written

Output: `.sddw/<feature>/requirements.md`
Sections: Purpose, User Stories (As a / I want / so that), Functional Requirements (FR-01, FR-02...), Acceptance Criteria, Constraints

Rules: SHALL use RFC 2119 keywords (SHALL/SHOULD/MAY/SHALL NOT), no implementation details, every FR atomic + testable + user-centric.

### Step 2: Code Analysis (optional)

`/sddw:code-analysis <feature> [--auto]`

Scan existing codebase for patterns, interfaces, flows, conventions. Grounding design in reality. Output: `.sddw/code-analysis.md`. Skip for greenfield projects.

### Step 3: Design

`/sddw:design <feature> [--auto]`

Produce cross-cutting `design.md` — shared architecture, data models, interface contracts, design decisions. Every element traces to FR-IDs.

- Every FR appears in at least one design element
- Documents rationale + rejected alternatives
- Combined alias: `/sddw:design_and_taskify <feature> [--auto]`

Output: `.sddw/<feature>/design/design.md`

### Step 4: Taskify

`/sddw:taskify <feature> [--auto]`

Break feature into hybrid task files referencing `design.md`. Each task file is self-contained (files, criteria) + references design for architecture. In dependency order.

Output:
```
.sddw/<feature>/design/
├── design.md
└── tasks/
    ├── task-1-<slug>.md
    └── task-2-<slug>.md
```

### Step 5: Implement

`/sddw:implement <feature> --task <N> [--auto]`

Execute one task at a time:

- **TDD Protocol**: RED (failing test) → GREEN (minimal pass) → REFACTOR
  - TDD for business logic/APIs/validation; skip for config/glue
  - Heuristic: can you write `expect(fn(input)).toBe(output)` before `fn`? If yes → TDD
- **Commit Protocol**: one task = one commit, after tests pass
- **Deviation Handling**: 4 rules (typo → fix; design gap → ask user; spec gap → update task file first; architectural → always ask user even in `--auto`)

After each task: writes `task-N-<slug>.done.md` completion report to `implement/tasks/`.

### Step 6: Verify

`/sddw:verify <feature> [--auto]`

Cross-check implementation against requirements:

- Load artifacts, detect test runner, check task completion status
- Run test suite, cross-check each FR against acceptance criteria
- Produce verification report

Output: `.sddw/<feature>/verify/report.md` — FR-by-FR pass/fail, test results, deviations, warnings.

Remediation loop: issues → remediation task files → implement → verify (repeat until all pass).

### Step 7: Self-Improve

`/sddw:self-improve <feature> [--auto]`

Analyse execution across all steps. Identify what went wrong and propose concrete improvements to the workflow itself:

- **Analyse** — extract signals: deviations, difficulties, remediation task origins, spec gaps
- **Diagnose** — classify by step, identify patterns
- **Apply** — diff previews, apply approved changes to workflow files

Output: `.sddw/<feature>/self-improve/report.md`. The workflow evolves with every feature — gaps found during one feature prevent the same issues in the next.

## Interaction Modes

| Mode | Behavior |
|------|----------|
| **Interactive** (default) | Full guided dialog, one question at a time, user confirms every spec block |
| **`--auto`** | Fully autonomous, no questions, best-judgment decisions (vibecoding mode) |

## Modular Architecture

Every step is assembled from four reusable components:

| Component | Purpose | Folder |
|-----------|---------|--------|
| **Command** | Thin entry point with `@references` | `commands/` |
| **Instructions** | Process rules — what to do, in what order | `instructions/` |
| **Questionnaire** | Dialog guidance — how to interact with the user | `questionnaires/` |
| **Specs** | Output format templates — what to produce | `specs/` |

```
commands/<step>.md
│
├── @instructions/<step>.md   ← process rules
├── @questionnaires/<step>.md ← dialog flow
└── @specs/<step>.md          ← output format

reads:  .sddw/<feature>/<previous_step>.md ← input
writes: .sddw/<feature>/<current_step>.md  ← output
```

Each component lives independently so they can be reused, tested, and evolved without touching others. Command files stay minimal.

Every step follows the three-phase dialog: **Discover → Research & Propose → Confirm & Generate**.

## Additional Commands

| Command | Purpose |
|---------|---------|
| `/sddw:chat <feature> [--auto]` | Fast-track with existing artifacts; skip questionnaire ceremony |
| `/sddw:help [list | status <feature>]` | Workflow overview, list features, check feature status |

## Output Structure

```
.sddw/
├── code-analysis.md           # shared across features
└── <feature>/
    ├── requirements.md
    ├── design/
    │   ├── design.md
    │   └── tasks/
    │       ├── task-1-<slug>.md
    │       └── task-N-<slug>.md
    ├── implement/
    │   └── tasks/
    │       └── task-N-<slug>.done.md
    ├── verify/
    │   └── report.md
    └── self-improve/
        └── report.md
```

## Design Principles

1. **Specs as primary artifact** — reviewable by peers, version-controlled, persistent across sessions
2. **Focused context** — `/clear` between steps, each step works in a focused context window
3. **Remediation loop** — verify → remediation tasks → implement → verify (loop until all pass)
4. **Self-improving** — workflow evolves with every feature
5. **Modular components** — instructions, questionnaires, specs evolve independently

## Installation

```bash
git clone https://github.com/sermakarevich/sddw.git ~/.claude/sddw
cd ~/.claude/sddw && bash bin/install.sh
```

Development (symlink from local repo):
```bash
git clone https://github.com/sermakarevich/sddw.git
cd sddw && bash bin/install.sh --local
```

## Relationship to SDD Tool Landscape

sddw is one of the most structured SDD tools — uniquely combining:
- **Step 7 (Self-Improve)** — only SDD tool with a self-improvement feedback loop
- **Modular component architecture** — commands/instructions/questionnaires/specs as independent folders
- **Remediation loop** — formal verify → remediate → re-verify cycle
- **Dual TDD + Commit protocols** — enforces test-first per task

Compared to other tools in the landscape:
- More structured than OpenSpec (which focuses on delta spec format) — covers full lifecycle
- More opinionated than Spec-Kit (which is programmable/pipeline-based) — opinionated dialog flow
- More self-improving than Swarm (which focuses on worktree isolation) — meta-level workflow evolution

## Meta

- **Language**: Shell + Markdown (no runtime dependencies)
- **License**: MIT
- **Integration**: Claude Code (`~/.claude/sddw/`)
- **Stars**: 56 (as of 2026-05-24)
- **Created**: 2026-03-18
