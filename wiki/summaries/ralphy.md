---
title: ralphy
type: summary
created: 2026-05-26
updated: 2026-05-26
sources: ["raw/refs/ralphy/README.md"]
tags: ["autonomous-loop", "multi-engine", "parallel-execution"]
---

# Summary — michaelshimeles/ralphy

Ralph Wiggum 模式的增强版 CLI：8 种 AI 引擎 + 并行执行 + GitHub Issues 任务源 + sandbox 模式 + 浏览器自动化。npm 一条命令安装。

## 核心要点

1. **8 种引擎**：Claude / Codex / OpenCode / Cursor / Qwen / Droid / Copilot / Gemini
2. **并行执行**：独立 worktree + 分支，支持 sandbox 模式（symlink 共享依赖）
3. **多任务源**：Markdown / YAML / JSON / GitHub Issues / 文件夹
4. **项目配置**：.ralphy/config.yaml 管理 rules / boundaries / commands
5. **实用场景**：修单元测试、从 GitHub Issues 拉任务、并行开发多个功能

## 与 wiki 其他实体的关联

- [[entities/concepts-frameworks/ralph]] — 原版 snarktank/ralph（19.5k stars）
- [[entities/concepts-frameworks/ralph-claude-code]] — 早期实现
- [[concepts/Harness Engineering]] — 自主循环 + 反馈闭环的工程化实践

## 相关链接

- GitHub: https://github.com/michaelshimeles/ralphy
- npm: https://www.npmjs.com/package/ralphy-cli
