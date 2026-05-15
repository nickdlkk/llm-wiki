---
title: github/spec-kit
type: entity
created: 2026-04-17
updated: 2026-04-17
sources:
  - github:github/spec-kit
tags:
  - spec-driven-development
  - sdd
  - python
  - workflow-engine
---

# Spec-Kit (GitHub) — Entity Page

## Overview

**Spec-Kit** is GitHub's toolkit for Spec-Driven Development (SDD), written in Python. It provides a comprehensive workflow engine for orchestrating multi-step automation pipelines using YAML-defined workflows.

**GitHub:** `github/spec-kit`
**Language:** Python
**License:** MIT

> *"Toolkit to help you get started with Spec-Driven Development."*

---

## Architecture

### Directory Structure

```
src/specify_cli/
├── __init__.py
├── agents.py              # Agent wrapper abstractions
├── integrations/           # Per-tool integration subpackages
│   ├── __init__.py        # INTEGRATION_REGISTRY + _register_builtins()
│   ├── base.py             # IntegrationBase, MarkdownIntegration, TomlIntegration, SkillsIntegration
│   ├── manifest.py         # IntegrationManifest (file tracking)
│   ├── claude/             # Claude Code integration
│   │   ├── __init__.py
│   │   └── scripts/        # Thin wrapper scripts
│   ├── gemini/             # Gemini CLI integration
│   ├── windsurf/           # Windsurf integration
│   ├── copilot/            # GitHub Copilot integration
│   └── ... (22 integrations)
├── workflows/
│   ├── __init__.py         # WorkflowEngine + STEP_REGISTRY
│   ├── base.py             # StepBase, StepContext, StepResult
│   ├── engine.py            # Workflow execution engine
│   ├── catalog.py           # Workflow catalog
│   ├── expressions.py       # Expression evaluation
│   └── steps/               # Step type implementations
│       ├── command.py       # CommandStep
│       ├── prompt.py        # PromptStep
│       ├── shell.py         # ShellStep
│       ├── gate.py          # GateStep
│       ├── if_then.py       # IfThenStep
│       ├── switch.py        # SwitchStep
│       ├── fan_in.py        # FanInStep
│       ├── fan_out.py       # FanOutStep
│       ├── while_loop.py    # WhileStep
│       └── do_while.py      # DoWhileStep
├── presets.py             # Built-in workflow presets
└── extensions.py          # Extension system
```

---

## Integration Architecture

### Base Classes (`integrations/base.py`)

```
IntegrationOption      — dataclass: CLI option declaration
IntegrationBase(ABC)   — abstract base; must implement install()/teardown()
  ├── MarkdownIntegration — standard .md command format (most common)
  ├── TomlIntegration    — TOML-format commands (Gemini, Tabnine)
  └── SkillsIntegration  — skill directories (speckit-<name>/SKILL.md)
```

### Adding a New Integration

1. **Choose base class** (MarkdownIntegration for most tools)
2. **Create subpackage** at `integrations/<key>/__init__.py`
3. **Implement class** with 3 required class attrs:

```python
class WindsurfIntegration(MarkdownIntegration):
    key = "windsurf"
    config = {
        "name": "Windsurf",
        "folder": ".windsurf/",
        "commands_subdir": "workflows",
        "install_url": None,
        "requires_cli": False,
    }
    registrar_config = {
        "dir": ".windsurf/workflows",
        "format": "markdown",
        "args": "$ARGUMENTS",
        "extension": ".md",
    }
    context_file = ".windsurf/rules/specify-rules.md"
```

4. **Register** in `integrations/__init__.py`

### Integration Registry

`INTEGRATION_REGISTRY` is the single source of truth for Python-side metadata. Shared dispatcher scripts (`scripts/bash/update-agent-context.sh`) maintain their own agent-key→context-file mappings until migrated to registry-based dispatch.

---

## Workflow Engine

### Step Types (10 built-in)

| Step | Purpose |
|------|---------|
| `CommandStep` | Execute a CLI command |
| `PromptStep` | Interact with AI model |
| `ShellStep` | Run shell commands |
| `GateStep` | Conditional branching (gate: all must pass) |
| `IfThenStep` | Simple if/else |
| `SwitchStep` | Multi-way branching |
| `FanInStep` | Wait for multiple parallel branches |
| `FanOutStep` | Parallel execution |
| `WhileStep` | While loop |
| `DoWhileStep` | Do-while loop |

### Workflow YAML Example

```yaml
name: spec-driven-example
steps:
  - type: prompt
    id: clarify
    prompt: "What feature do you want to build?"

  - type: gate
    id: validate
    conditions:
      - "clarify.response is not None"

  - type: fan_out
    id: parallel_specs
    steps:
      - type: command
        command: "specify spec {{ item }}"
      items: "{{ features }}"

  - type: fan_in
    wait_for: parallel_specs

  - type: prompt
    id: implement
    prompt: "Implement the specs in parallel_specs"
```

### WorkflowEngine

Loads YAML workflow definitions, validates step graph (no cycles, referenced steps exist), then executes steps in dependency order with context passing between steps.

---

## Comparison with OpenSpec

| Dimension | Spec-Kit | OpenSpec |
|-----------|----------|----------|
| Language | Python | TypeScript |
| Workflow model | YAML-defined programmable pipelines (FanIn/FanOut/loops) | Fixed workflow templates (propose/apply/archive) |
| AI tools | 22 integrations | 24+ tools via adapter registry |
| Spec format | Custom Markdown with frontmatter | Delta-formatted Markdown (ADDED/MODIFIED/REMOVED sections) |
| Artifact tracking | YAML manifest per change | `.openspec.yaml` + JSON status |
| Learning curve | Steeper (workflow YAML, step types) | Lower (simple commands, familiar Markdown) |
| Flexibility | Higher (programmable workflows) | Lower (opinionated templates) |
| Setup | Interactive CLI | `openspec init` |

**Key insight:** Spec-Kit is more *programmable* (workflow-as-code), OpenSpec is more *opinionated* (workflow-as-convention). Spec-Kit suits teams with specific processes; OpenSpec suits individuals wanting a lightweight spec layer.

---

## Key Source Files

| Path | Purpose |
|------|---------|
| `src/specify_cli/integrations/base.py` | Integration base classes |
| `src/specify_cli/integrations/__init__.py` | INTEGRATION_REGISTRY + _register_builtins() |
| `src/specify_cli/workflows/__init__.py` | WorkflowEngine + STEP_REGISTRY |
| `src/specify_cli/workflows/engine.py` | Workflow execution engine |
| `src/specify_cli/workflows/base.py` | StepBase, StepContext, StepResult |
| `src/specify_cli/workflows/steps/*.py` | 10 step type implementations |
| `src/specify_cli/integrations/claude/__init__.py` | Claude Code integration (example) |
