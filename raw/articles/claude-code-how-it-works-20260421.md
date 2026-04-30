---
source: https://code.claude.com/docs/en/how-claude-code-works
title: How Claude Code Works — Claude Code Docs
authors: Anthropic
date: 2026-04-21
tags: [claude, anthropic, agentic-loop, agentic-harness, tools, context-window, checkpoints, permissions, subagents, skills]
url: https://code.claude.com/docs/en/how-claude-code-works
---

# How Claude Code Works

Understand the agentic loop, built-in tools, and how Claude Code interacts with your project.

## The Agentic Loop

When you give Claude a task, it works through three phases: **gather context**, **take action**, and **verify results**. These phases blend together. Claude uses tools throughout, whether searching files to understand your code, editing to make changes, or running tests to check its work.

The loop adapts to what you ask. A question about your codebase might only need context gathering. A bug fix cycles through all three phases repeatedly. A refactor might involve extensive verification. Claude decides what each step requires based on what it learned from the previous step, chaining dozens of actions together and course-correcting along the way.

You're part of this loop too. You can interrupt at any point to steer Claude in a different direction, provide additional context, or ask it to try a different approach. Claude works autonomously but stays responsive to your input.

**The agentic loop is powered by two components: models that reason and tools that act.** Claude Code serves as the **agentic harness** around Claude: it provides the tools, context management, and execution environment that turn a language model into a capable coding agent.

## Models

Claude Code uses Claude models to understand your code and reason about tasks. Claude can read code in any language, understand how components connect, and figure out what needs to change to accomplish your goal. For complex tasks, it breaks work into steps, executes them, and adjusts based on what it learns.

Multiple models are available with different tradeoffs. Sonnet handles most coding tasks well. Opus provides stronger reasoning for complex architectural decisions.

## Tools

Tools are what make Claude Code agentic. Without tools, Claude can only respond with text. With tools, Claude can act: read your code, edit files, run commands, search the web, and interact with external services.

The built-in tools fall into five categories:

| Category | What Claude can do |
|----------|-------------------|
| **File operations** | Read files, edit code, create new files, rename and reorganize |
| **Search** | Find files by pattern, search content with regex, explore codebases |
| **Execution** | Run shell commands, start servers, run tests, use git |
| **Web** | Search the web, fetch documentation, look up error messages |
| **Code intelligence** | See type errors and warnings after edits, jump to definitions, find references |

Example tool chain for "fix the failing tests":
1. Run the test suite to see what's failing
2. Read the error output
3. Search for the relevant source files
4. Read those files to understand the code
5. Edit the files to fix the issue
6. Run the tests again to verify

## What Claude Can Access

When you run `claude` in a directory, Claude Code gains access to:
- **Your project.** Files in your directory and subdirectories
- **Your terminal.** Any command you could run: build tools, git, package managers, system utilities, scripts
- **Your git state.** Current branch, uncommitted changes, and recent commit history
- **Your CLAUDE.md.** A markdown file where you store project-specific instructions, conventions, and context
- **Auto memory.** Learnings Claude saves automatically as you work
- **Extensions you configure.** MCP servers, skills, subagents, and Claude in Chrome

## Execution Environments

Claude Code runs in three environments:
- **Local** — Your machine. Default. Full access to your files, tools, and environment
- **Cloud** — Anthropic-managed VMs. Offload tasks, work on repos you don't have locally
- **Remote Control** — Your machine, controlled from a browser. Use the web UI while keeping everything local

## Sessions and Context

Claude Code saves your conversation locally as you work. Each message, tool use, and result is written to a plaintext JSONL file under `~/.claude/projects/`, which enables rewinding, resuming, and forking sessions. Before Claude makes code changes, it also snapshots the affected files so you can revert if needed.

**Sessions are independent.** Each new session starts with a fresh context window, without the conversation history from previous sessions. Claude can persist learnings across sessions using auto memory, and you can add your own persistent instructions in CLAUDE.md.

**Context window management**: Claude's context window holds your conversation history, file contents, command outputs, CLAUDE.md, auto memory, loaded skills, and system instructions. As context fills up, Claude compacts automatically. It clears older tool outputs first, then summarizes the conversation if needed. Put persistent rules in CLAUDE.md rather than relying on conversation history.

## Safety: Checkpoints and Permissions

**Checkpoints**: Every file edit is reversible. Before Claude edits any file, it snapshots the current contents. If something goes wrong, press `Esc` twice to rewind to a previous state, or ask Claude to undo. Checkpoints are local to your session, separate from git. They only cover file changes.

**Permission modes** (Shift+Tab to cycle):
- **Default** — Claude asks before file edits and shell commands
- **Auto-accept edits** — Claude edits files and runs common filesystem commands without asking, still asks for other commands
- **Plan mode** — Claude uses read-only tools only, creating a plan you can approve before execution
- **Auto mode** — Claude evaluates all actions with background safety checks. Currently a research preview

## Work Effectively

Key principles from the docs:

1. **Be specific upfront** — The more precise your initial prompt, the fewer corrections you'll need
2. **Give Claude something to verify against** — Tests, screenshots, or expected outputs let Claude check its own work
3. **Explore before implementing** — Use Plan Mode to separate research from coding
4. **Delegate, don't dictate** — Give context and direction, then trust Claude to figure out the details
5. **Course-correct early and often** — Correct Claude as soon as you notice it going off track
6. **Manage context aggressively** — Run `/clear` between unrelated tasks

## Extend with Features

The built-in tools are the foundation. You can extend what Claude knows with:
- **Skills** — Specialized capabilities defined in Markdown, loaded on demand
- **MCP** — Connect to external services like Notion, Figma, or your database
- **Hooks** — Run scripts automatically at specific points in Claude's workflow
- **Subagents** — Spawn specialized assistants for isolated tasks with their own context

---

# Key Insight: The Agentic Harness Definition

> **Claude Code serves as the agentic harness around Claude: it provides the tools, context management, and execution environment that turn a language model into a capable coding agent.**

This is the canonical definition of "agentic harness" from Anthropic's official documentation. The harness is not the model itself — it's the entire infrastructure surrounding the model:
- Tools (file ops, search, execution, web, code intelligence)
- Context management (session persistence, checkpoints, compaction)
- Execution environment (local/cloud/remote)
- Safety mechanisms (checkpoints, permissions)
- Extension system (skills, MCP, hooks, subagents)
