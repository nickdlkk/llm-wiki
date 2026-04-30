---
source: https://docs.anthropic.com/en/docs/agent-sdk/overview
title: Agent SDK Overview — Anthropic
authors: Anthropic
date: 2026-04-21
tags: [anthropic, agent-sdk, python, typescript, tools, hooks, subagents, mcp, permissions, sessions]
url: https://docs.anthropic.com/en/docs/agent-sdk/overview
---

# Agent SDK Overview

Build AI agents that autonomously read files, run commands, search the web, edit code, and more. The Agent SDK gives you the same tools, agent loop, and context management that power Claude Code, programmable in Python and TypeScript.

## Core Components

The Agent SDK includes everything that makes Claude Code powerful:

### Built-in Tools

| Tool | What it does |
|------|--------------|
| **Read** | Read any file in the working directory |
| **Write** | Create new files |
| **Edit** | Make precise edits to existing files |
| **Bash** | Run terminal commands, scripts, git operations |
| **Monitor** | Watch a background script and react to each output line as an event |
| **Glob** | Find files by pattern (`**/*.ts`, `src/**/*.py`) |
| **Grep** | Search file contents with regex |
| **WebSearch** | Search the web for current information |
| **WebFetch** | Fetch and parse web page content |
| **AskUserQuestion** | Ask the user clarifying questions with multiple choice options |

### Hooks

Run custom code at key points in the agent lifecycle. SDK hooks use callback functions to validate, log, block, or transform agent behavior.

**Available hooks:** `PreToolUse`, `PostToolUse`, `Stop`, `SessionStart`, `SessionEnd`, `UserPromptSubmit`, and more.

This example logs all file changes to an audit file.

### Subagents

Spawn specialized agents to handle focused subtasks. Your main agent delegates work, and subagents report back with results.

Define custom agents with specialized instructions. Include `Agent` in `allowedTools` since subagents are invoked via the Agent tool.

Messages from within a subagent's context include a `parent_tool_use_id` field, letting you track which messages belong to which subagent execution.

### MCP (Model Context Protocol)

Control exactly which tools your agent can use. Allow safe operations, block dangerous ones, or require approval for sensitive actions.

Example: Create a read-only agent that can analyze but not modify code. `allowed_tools` pre-approves `Read`, `Glob`, and `Grep`.

### Permissions

Control exactly which tools your agent can use. Allow safe operations, block dangerous ones, or require approval for sensitive actions.

### Sessions

Maintain context across multiple exchanges. Claude remembers files read, analysis done, and conversation history. Resume sessions later, or fork them to explore different approaches.

## Claude Code Features in SDK

The SDK also supports Claude Code's filesystem-based configuration:

| Feature | Description | Location |
|---------|-------------|----------|
| **Skills** | Specialized capabilities defined in Markdown | `.claude/skills/*/SKILL.md` |
| **Slash commands** | Custom commands for common tasks | `.claude/commands/*.md` |
| **Memory** | Project context and instructions | `CLAUDE.md` or `.claude/CLAUDE.md` |
| **Plugins** | Extend with custom commands, agents, and MCP servers | Programmatic via `plugins` option |

## Agent SDK vs Client SDK

| Use case | Best choice |
|----------|-------------|
| Interactive development | CLI |
| CI/CD pipelines | SDK |
| Custom applications | SDK |
| One-off tasks | CLI |
| Production automation | SDK |

**Client SDK** gives you direct API access: you send prompts and implement tool execution yourself. **Agent SDK** gives you Claude with built-in tool execution.

With the Client SDK, you implement a tool loop. With the Agent SDK, Claude handles it.

Many teams use both: CLI for daily development, SDK for production. Workflows translate directly between them.

## Architecture Summary

```
Agent = Model (reasoning) + Harness (everything else)

Harness components:
├── Tools (Read, Write, Edit, Bash, etc.)
├── Hooks (PreToolUse, PostToolUse, Stop, etc.)
├── Subagents (spawn with own context)
├── MCP (Model Context Protocol for external tools)
├── Permissions (allowlist/blocklist per tool)
├── Sessions (persist across multiple exchanges)
└── Filesystem-based config (CLAUDE.md, skills, commands)
```
