---
title: OpenSpec
type: concept
created: 2026-04-17
updated: 2026-04-17
sources:
  - github:Fission-AI/OpenSpec
tags:
  - spec-driven-development
  - sdd
  - ai-coding-agent
  - workflow
---

# OpenSpec — Spec-Driven Development for AI Agents

## Overview

**OpenSpec** is a lightweight Spec-Driven Development (SDD) framework for AI coding assistants. It creates a structured specification layer between human intent and code implementation — so human and AI align on *what to build* before any code is written.

> *"AI coding assistants are powerful but unpredictable when requirements live only in chat history."* — OpenSpec README

**npm:** `@fission-ai/openspec`
**GitHub:** `Fission-AI/OpenSpec` (TypeScript, MIT license)

### Philosophy

```
→ fluid not rigid
→ iterative not waterfall
→ easy not complex
→ built for brownfield not just greenfield
→ scalable from personal projects to enterprises
```

### vs Spec-Kit (GitHub)

| Dimension | Spec-Kit | OpenSpec |
|-----------|----------|----------|
| Language | Python CLI | TypeScript CLI |
| Phase gates | Rigid waterfall | Iterative, fluid |
| AI tools | GitHub Copilot only | 24+ tools (Claude Code, Cursor, etc.) |
| Setup | Heavy, many files | Lightweight, one command |
| Workflow | Sequential phases | Artifact-guided, parallel-ready |

---

## Architecture

### Directory Structure

```
project/
├── openspec/
│   ├── changes/
│   │   └── <change-name>/
│   │       ├── proposal.md         # what & why
│   │       ├── design.md            # how (technical approach)
│   │       ├── tasks.md             # implementation checklist
│   │       ├── specs/
│   │       │   └── <capability>/
│   │       │       └── spec.md      # delta-formatted spec changes
│   │       └── .openspec.yaml       # change metadata + artifact tracking
│   ├── specs/
│   │   └── <capability>/
│   │       └── spec.md              # main spec (accumulates deltas)
│   └── (global config)
```

### Core Data Model

#### Spec Schema

```typescript
Spec = {
  name: string,           // e.g., "User Authentication"
  overview: string,       // one-liner purpose
  requirements: Requirement[]
}
```

#### Requirement Schema

```typescript
Requirement = {
  text: string,           // MUST/SHALL language — validated by Zod
  scenarios: Scenario[]   // at least 1 scenario required
}

Scenario = { rawText: string }
```

> **Validation rule:** Requirement text MUST contain `SHALL` or `MUST`. Scenarios are required — each requirement needs at least one concrete test scenario.

#### Change Schema

```typescript
Change = {
  name: string,
  why: string,            // 10–500 chars — explains motivation
  whatChanges: string,     // summary of scope
  deltas: Delta[]          // ADDED | MODIFIED | REMOVED | RENAMED
}
```

#### Delta Schema

```typescript
Delta = {
  spec: string,           // capability name
  operation: 'ADDED' | 'MODIFIED' | 'REMOVED' | 'RENAMED',
  description: string,
  requirement?: Requirement,
  rename?: { from: string; to: string }
}
```

---

## Spec Format

### Main Spec File

```markdown
# User Authentication Specification

## Purpose
Handles user signup, login, logout, and session management.

## Requirements

### Requirement: Password Requirements
Passwords SHALL be at least 12 characters and contain uppercase, lowercase, digits, and symbols.

#### Scenarios
**Scenario:** Registration with valid password
Given the registration form
When user enters "SecurePass123!"
Then account is created successfully
```

### Delta Spec File (inside change)

```markdown
# User Authentication — Add OAuth

## What Changes
Adds Google OAuth as an alternative login method.

## ADDED Requirements

### Requirement: Google OAuth Integration
The system SHALL support Google OAuth 2.0 authentication flow.

#### Scenarios
**Scenario:** Login with Google
Given the login page
When user clicks "Sign in with Google"
Then OAuth redirect to Google occurs

## MODIFIED Requirements

### Requirement: Session Management
Sessions SHALL be persisted in Redis with 24-hour TTL and sliding expiration.
(Original: "Sessions SHALL be stored in memory with 1-hour TTL.")
```

---

## Workflow System

### Core Workflows (11 total)

| Workflow | Purpose |
|----------|---------|
| `propose` | Create change + all artifacts in one step (new artifact-guided approach) |
| `explore` | Understand existing codebase before writing specs |
| `new` | Create change scaffold + sequential artifact creation |
| `continue` | Continue building pending artifacts |
| `apply` | Implement tasks from tasks.md |
| `ff` | Fast-forward: apply + archive in one command |
| `sync` | Sync spec changes across branches |
| `archive` | Complete change: apply deltas to main specs, archive change |
| `verify` | Verify implementation matches spec |
| `bulk-archive` | Archive multiple completed changes |
| `onboard` | Bootstrap OpenSpec in new project |

### Artifact-Guided Workflow (new `/opsx:propose`)

The new propose workflow is **artifact-driven** rather than phase-driven:

```
User: /opsx:propose "add dark mode"
AI:   Creates openspec/changes/add-dark-mode/
      ✓ proposal.md — what & why
      ✓ specs/     — requirements + scenarios
      ✓ design.md  — technical approach
      ✓ tasks.md   — implementation checklist
      Ready for implementation!

User: /opsx:apply
AI:   Implements tasks...
      ✓ 1.1 Add theme context provider
      ✓ 1.2 Create toggle component
      All tasks complete!

User: /opsx:archive
AI:   Archived. Specs updated.
```

The AI uses `openspec status --change <name> --json` to track artifact dependencies and `openspec instructions <artifact-id> --change <name> --json` to get per-artifact instructions.

---

## CLI Architecture

### Command Tree

```
openspec init [path]           # Initialize project
openspec update [path]         # Refresh skills/commands for all tools
openspec list                  # List changes
openspec list --specs          # List specs
openspec view                  # Interactive TUI dashboard
openspec archive [name]        # Archive change, apply deltas
openspec validate [name]       # Validate changes/specs
openspec show [name]           # Show change or spec
openspec new change <name>     # Create change scaffold
openspec status                # Show artifact status
openspec instructions [artifact] # Get artifact creation instructions
openspec feedback <message>    # Submit feedback
openspec config                # Configure profile/delivery
```

### Profile System

Profiles control **which** workflows are active:

```typescript
const CORE_WORKFLOWS = ['propose', 'explore', 'apply', 'archive'];
const ALL_WORKFLOWS = [
  'propose', 'explore', 'new', 'continue', 'apply',
  'ff', 'sync', 'archive', 'bulk-archive', 'verify', 'onboard'
];
```

- `core` profile: 4 workflows (new user default)
- `custom` profile: any subset

**Delivery** controls **how** artifacts are delivered:
- `both` — skills + commands (default)
- `skills` — skill directories only
- `commands` — slash command files only

---

## Multi-Agent Integration

OpenSpec supports **24+ AI coding tools** via a registry pattern. Detection is auto-discovery based on tool-specific directories.

### Supported Tools (partial)

| Tool | Skills Dir | Adapter |
|------|-----------|---------|
| Claude Code | `.claude` | YAML frontmatter |
| Cursor | `.cursor` | Custom markdown |
| Windsurf | `.windsurf` | Markdown workflows |
| Gemini CLI | `.gemini` | TOML commands |
| GitHub Copilot | `.github` | Instructions file |
| Codex | `.codex` | Skill directories |
| OpenCode | `.opencode` | Hyphen commands |

Full list: `src/core/config.ts` → `AI_TOOLS[]`

### Integration Architecture

```
CommandAdapterRegistry (src/core/command-generation/registry.ts)
  └── 26 adapters (one per tool)
       ├── getFilePath(commandId) → file path for this tool
       └── formatFile(content)   → tool-specific file format
```

Example — Claude adapter (`src/core/command-generation/adapters/claude.ts`):
```typescript
formatFile(content: CommandContent): string {
  return `---\nname: ${content.name}\ndescription: ${content.description}\ncategory: ${content.category}\ntags: ${formatTagsArray(content.tags)}\n---\n\n${content.body}\n`;
}
```

### Skill Generation Pipeline

```
getSkillTemplates(workflowFilter)
  → SkillTemplateEntry[] (dirName + workflowId + SkillTemplate)

generateSkillContent(template, version, transformer?)
  → writes .claude/commands/opsx/<id>.md (or equivalent for other tools)
```

---

## Spec Application (Delta Merge)

`src/core/specs-apply.ts` — the core delta merge logic:

```
applySpecs(changeName, options?)
  1. findSpecUpdates(changeDir, mainSpecsDir)
  2. buildUpdatedSpec(update)  ← validation pass, no writes
  3. Validator.validateSpecContent() ← validate rebuilt spec
  4. writeUpdatedSpec()        ← apply to main spec
```

### Operation Order (critical)

Delta operations apply in this order:
1. **RENAMED** — update header names (new name must not collide)
2. **REMOVED** — delete requirements
3. **MODIFIED** — replace requirement content
4. **ADDED** — insert new requirements

### Duplicate/Conflict Detection

Before applying, `buildUpdatedSpec()` validates:
- No duplicate names within same delta section
- No cross-section conflicts (e.g., MODIFIED + REMOVED for same name)
- For new specs: only ADDED allowed; MODIFIED/RENAMED are errors

### Preservation

Original ordering of existing requirements is preserved; newly added requirements append at the end.

---

## Parsing: Requirement Blocks

`src/core/parsers/requirement-blocks.ts`:

```typescript
extractRequirementsSection(content: string): RequirementsSectionParts
  → splits content on ## Requirements boundary
  → extracts preamble + RequirementBlock[]

parseDeltaSpec(content: string): DeltaPlan
  → splits on ## ADDED/MODIFIED/REMOVED/RENAMED Requirements
  → returns { added, modified, removed, renamed, sectionPresence }
```

**Key parsing rules:**
- Fenced code blocks are stripped but their line count is preserved (prevents false section boundary detection)
- Requirement header: `### Requirement: <name>` at `##` depth, inside `## Requirements`
- Delta header: `## ADDED Requirements` (not valid in main spec)

---

## Validation

`src/core/validation/` — Zod-based validation:

```typescript
Validator.validateSpecContent(name, content) → ValidationReport
  ├── valid: boolean
  └── issues: ValidationIssue[]

// Validates:
// - Requirements have SHALL/MUST
// - Each requirement has ≥1 scenario
// - Scenarios are non-empty
// - Why section length (10–500 chars)
// - Deltas don't conflict
```

---

## Telemetry

`src/telemetry/` — Anonymous usage stats (opt-out: `OPENSPEC_TELEMETRY=0`):

```typescript
trackCommand(commandPath: string, version: string)
// Only command names + version. No arguments, no paths, no PII.
// Auto-disabled in CI.
```

---

## Key Source Files

| Path | Purpose |
|------|---------|
| `src/core/change.ts` | Change data model (types exported from schemas) |
| `src/core/schemas/*.ts` | Zod schemas for Spec, Change, Delta, Requirement |
| `src/core/parsers/requirement-blocks.ts` | Spec/delta Markdown parser |
| `src/core/parsers/spec-structure.ts` | Spec structure validation |
| `src/core/specs-apply.ts` | Delta → main spec merge engine |
| `src/core/update.ts` | `openspec update` command — refresh skills/commands |
| `src/core/init.ts` | `openspec init` — setup wizard |
| `src/core/config.ts` | AI_TOOLS registry (26 tools) |
| `src/core/profiles.ts` | CORE_WORKFLOWS, ALL_WORKFLOWS, profile resolver |
| `src/core/command-generation/registry.ts` | Tool adapter registry |
| `src/core/command-generation/adapters/*.ts` | Per-tool formatting (26 adapters) |
| `src/core/shared/skill-generation.ts` | Skill + command template generation |
| `src/core/templates/workflows/*.ts` | 11 workflow templates (propose, apply, etc.) |
| `src/core/validation/` | Zod validator + message constants |
| `src/cli/index.ts` | Commander.js CLI entry point |

---

## OpenSpec vs Hermes Harness Patterns

| Dimension | OpenSpec | Hermes (Harness Engineering) |
|-----------|----------|-------------------------------|
| Focus | Spec-driven dev (requirements first) | Agent reliability engineering |
| Artifact | Markdown specs, proposals, tasks | Trajectories, context compression |
| Workflow | Human-AI spec alignment before coding | Multi-layer feedback (compile/test/e2e/CI) |
| Agent integration | 24+ tools via slash commands | Tool call orchestration, subagent spawning |
| Iterative | Yes — fluid updates to any artifact | Yes — Four-Layer Feedback Loop |
| Scope | Feature-level specs + implementation | Full agent lifecycle management |

**Complementary:** OpenSpec provides the *spec layer* that could feed into a Hermes-based agent workflow — use OpenSpec's `/opsx:propose` to create specs, then use Hermes for the implementation loop with CI feedback.
