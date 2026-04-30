---
source: https://code.claude.com/docs/en/best-practices
title: Best Practices for Claude Code — Claude Code Docs
authors: Anthropic
date: 2026-04-21
tags: [claude, best-practices, agentic-loop, claude-md, skills, subagents, hooks, permissions, context-window, verification, plan-mode]
url: https://code.claude.com/docs/en/best-practices
---

# Best Practices for Claude Code

Tips and patterns for getting the most out of Claude Code, from configuring your environment to scaling across parallel sessions.

## Core Constraint

Most best practices are based on one constraint: **Claude's context window fills up fast, and performance degrades as it fills.**

Claude's context window holds your entire conversation, including every message, every file Claude reads, and every command output. However, this can fill up fast. A single debugging session or codebase exploration might generate and consume tens of thousands of tokens.

Context window is the most important resource to manage.

## Give Claude a Way to Verify Its Work

This is the single highest-leverage thing you can do.

Claude performs dramatically better when it can verify its own work, like run tests, compare screenshots, and validate outputs. Without clear success criteria, it might produce something that looks right but actually doesn't work. You become the only feedback loop, and every mistake requires your attention.

| Strategy | Before | After |
|----------|--------|-------|
| **Provide verification criteria** | _"implement a function that validates email addresses"_ | _"write a validateEmail function. example test cases: user@example.com is true, invalid is false, user@.com is false. run the tests after implementing"_ |
| **Verify UI changes visually** | _"make the dashboard look better"_ | _"implement this design. take a screenshot of the result and compare it to the original. list differences and fix them"_ |
| **Address root causes, not symptoms** | _"the build is failing"_ | _"the build fails with this error: [paste error]. fix it and verify the build succeeds. address the root cause, don't suppress the error"_ |

## Explore First, Then Plan, Then Code

Letting Claude jump straight to coding can produce code that solves the wrong problem. Use Plan Mode to separate exploration from execution.

Recommended workflow (4 phases):
1. **Explore** — Enter Plan Mode. Claude reads files and answers questions without making changes.
2. **Plan** — Ask Claude to create a detailed implementation plan.
3. **Implement** — Switch back to Normal Mode and let Claude code, verifying against its plan.
4. **Commit** — Ask Claude to commit with a descriptive message and create a PR.

Plan Mode is most useful when you're uncertain about the approach, when the change modifies multiple files, or when you're unfamiliar with the code being modified.

## Provide Specific Context

Claude can infer intent, but it can't read your mind. Reference specific files, mention constraints, and point to example patterns.

| Strategy | Before | After |
|----------|--------|-------|
| **Scope the task** | _"add tests for foo.py"_ | _"write a test for foo.py covering the edge case where the user is logged out. avoid mocks"_ |
| **Point to sources** | _"why does ExecutionFactory have such a weird api?"_ | _"look through ExecutionFactory's git history and summarize how its api came to be"_ |
| **Reference existing patterns** | _"add a calendar widget"_ | _"look at how existing widgets are implemented on the home page..."_ |

## Write an Effective CLAUDE.md

CLAUDE.md is a special file that Claude reads at the start of every conversation. Include Bash commands, code style, and workflow rules.

**Keep it short and human-readable.** Only include things that apply broadly. For domain knowledge or workflows that are only relevant sometimes, use skills instead.

| ✅ Include | ❌ Exclude |
|------------|------------|
| Bash commands Claude can't guess | Anything Claude can figure out by reading code |
| Code style rules that differ from defaults | Standard language conventions Claude already knows |
| Testing instructions and preferred test runners | Detailed API documentation (link to docs instead) |
| Repository etiquette (branch naming, PR conventions) | Information that changes frequently |
| Architectural decisions specific to your project | Long explanations or tutorials |
| Developer environment quirks (required env vars) | File-by-file descriptions of the codebase |
| Common gotchas or non-obvious behaviors | Self-evident practices like "write clean code" |

If Claude keeps doing something you don't want despite having a rule against it, the file is probably too long and the rule is getting lost.

## Configure Permissions

Three ways to reduce interruptions:
- **Auto mode** — A separate classifier model reviews commands and blocks only what looks risky. Best when you trust the general direction of a task.
- **Permission allowlists** — Permit specific tools you know are safe, like `npm run lint` or `git commit`.
- **Sandboxing** — Enable OS-level isolation that restricts filesystem and network access.

## Set Up Hooks

Hooks run scripts automatically at specific points in Claude's workflow. Unlike CLAUDE.md instructions which are advisory, hooks are deterministic and guarantee the action happens.

Example: _"Write a hook that runs eslint after every file edit"_ or _"Write a hook that blocks writes to the migrations folder."_

## Create Skills

Skills extend Claude's knowledge with information specific to your project, team, or domain. Claude applies them automatically when relevant, or you can invoke them directly with `/skill-name`.

Skills load on demand. Claude sees skill descriptions at session start, but the full content only loads when a skill is used. Set `disable-model-invocation: true` to keep descriptions out of context until you need them.

## Create Custom Subagents

Subagents run in their own context with their own set of allowed tools. They're useful for tasks that read many files or need specialized focus without cluttering your main conversation.

Subagents get their own fresh context, completely separate from your main conversation. Their work doesn't bloat your context. When done, they return a summary.

## Manage Context Aggressively

- Use `/clear` frequently between tasks to reset the context window entirely
- When auto compaction triggers, Claude summarizes what matters most
- For more control, run `/compact <instructions>`
- Customize compaction behavior in CLAUDE.md

## Run Non-Interactive Mode

With `claude -p "prompt"`, you can run Claude non-interactively, without a session. This is how you integrate Claude into CI pipelines, pre-commit hooks, or any automated workflow.

```bash
# One-off queries
claude -p "Explain what this project does"

# Structured output for scripts
claude -p "List all API endpoints" --output-format json

# Streaming for real-time processing
claude -p "Analyze this log file" --output-format stream-json
```

## Run Multiple Claude Sessions

Three main ways to run parallel sessions:
- **Claude Code desktop app** — Manage multiple local sessions visually
- **Claude Code on the web** — Run on Anthropic's secure cloud infrastructure in isolated VMs
- **Agent teams** — Automated coordination of multiple sessions with shared tasks, messaging, and a team lead

Quality-focused workflow: Use a Writer/Reviewer pattern with two sessions.

## Fan Out Across Files

For large migrations or analyses, distribute work across many parallel Claude invocations:

1. Have Claude list all files that need migrating
2. Write a script to loop through the list using `claude -p`
3. Test on a few files, then run at scale

Use `--allowedTools` to scope permissions for batch operations.

## Common Failure Patterns

- **The kitchen sink session** — One task, then unrelated questions, then back to first task. Context full of irrelevant info.
  > Fix: `/clear` between unrelated tasks.
- **Correcting over and over** — Claude does something wrong, you correct it, still wrong, you correct again.
  > Fix: After two failed corrections, `/clear` and write a better initial prompt.
- **The over-specified CLAUDE.md** — Too long, Claude ignores half of it.
  > Fix: Ruthlessly prune. If Claude already does something correctly without the instruction, delete it or convert to a hook.
- **The trust-then-verify gap** — Plausible-looking implementation that doesn't handle edge cases.
  > Fix: Always provide verification (tests, scripts, screenshots).
- **The infinite exploration** — Ask Claude to "investigate" without scoping it.
  > Fix: Scope investigations narrowly or use subagents.
