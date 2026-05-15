---
title: Aegis
type: entity
entity_type: tool
created: 2026-05-08
updated: 2026-05-08
sources: [GanyuanRan/Aegis-github, Aegis-readme]
tags: [harness-engineering, architecture-driven, ai-coding-agent, workflow, evidence-driven]
---

# Aegis

**Aegis** 是 Architecture-Driven Development (ADD) 方法包，为 AI 编程 Agent 提供**架构驱动的开发纪律**。基于 `superpowers` 方法论，叠加证据驱动治理、TLREF 执行流和双轨修复/退役规则。

GitHub: [GanyuanRan/Aegis](https://github.com/GanyuanRan/Aegis) — 113 ⭐, Shell 技术栈

## 解决的问题

AI 编程 Agent 擅长本地执行，但长任务或高风险软件工作常因**过程原因**失败：

- 任务边界和基线不清就开始改代码
- 声称完成但缺乏新鲜证据
- Bug fix 加新 fallback 逻辑，但老代码没人负责退役
- 长任务在 compaction/handoff/multi-agent 后丢失状态
- 架构漂移只在变更扩散后才被发现

## 核心机制

### ADD — Architecture-Driven Development

Agent 在做实质性变更前，应先理解：

- **Baseline** — 项目当前状态
- **架构边界** — 哪些模块/层可以改，哪些不能
- **Owner** — 谁对某块代码负责
- **影响面** — 改动会波及哪里
- **兼容性约束** — 向后兼容还是破坏性变更
- **验证路径** — 如何确认改动正确

### Governance Spine（治理主干）

| 机制 | 说明 |
|------|------|
| **Baseline-first** | 动手前先读懂项目基线 |
| **Evidence before claims** | 结论要有证据支撑 |
| **TLREF / DIVE / Reflection / QA** | 执行纪律模板 |
| **Repair track + Retirement track** | 修复和废弃一起管理 |
| **Long-task continuation** | todo checkpoint、resume hints、drift checks |

## 与 superpowers 的关系

保留 `superpowers` 的有用部分：
- 可组合技能（composable skills）
- 技能触发的工作流
- 多 host 安装和插件分发骨架
- 实现规划、审查、调试、验证实践

Aegis 在此基础上加入更严格的治理：
- Baseline-first 工作
- 证据驱动的结论
- 影响感知的任务 framing
- TLREF / DIVE / Reflection / QA 执行纪律
- Repair + Retirement 双轨
- 长任务延续机制

## Host 兼容性

| Host | 当前状态 |
|------|---------|
| Codex | ✅ 代表性 smoke path 已验证 |
| OpenCode | ✅ 基础套件和集成测试已通过 |
| Claude Code | 插件骨架存在，release 级 smoke 待验证 |
| CodeBuddy | 插件骨架存在 |
| DeepSeek-TUI | 支持手动安装 SKILL.md |
| Trae | 支持手动安装 SKILL.md |

## 安装

### Codex（macOS / Linux）

```bash
git clone https://github.com/GanyuanRan/Aegis.git ~/.codex/aegis
mkdir -p ~/.agents/skills
ln -s ~/.codex/aegis/skills ~/.agents/skills/aegis
```

### Codex（Windows PowerShell）

```powershell
git clone https://github.com/GanyuanRan/Aegis.git "$env:USERPROFILE\.codex\aegis"
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.agents\skills"
cmd /c mklink /J "$env:USERPROFILE\.agents\skills\aegis" "$env:USERPROFILE\.codex\aegis\skills"
```

装完后重启 Host，Agent 自动发现 Aegis 技能。

## 激活模式

Aegis 默认**自动模式**。切换为手动模式：

```toml
# ~/.config/aegis/config.toml（Linux/macOS）
# %USERPROFILE%\.config\aegis\config.toml（Windows）
activation_mode = "explicit"
```

设为 `"auto"` 或删除文件可恢复自动模式。

## 当前边界

**Aegis 当前负责**：
- 方法包技能
- 初始指令和贡献者护栏
- Host 安装指南
- 代表性测试和验证资产
- runtime-ready artifact 形状
- 发布、回滚、已知限制、兼容性清单

**Aegis 当前不负责**：
- Host Adapters
- Runtime Core
- 权威 `GateDecision`
- 最终完成授权
- 完整生产级 rollout 保证

## 相关概念

- [[Harness Engineering]] — 让 Agent 可靠工作的五大子系统
- [[concepts-frameworks/gstack]] — Garry Tan 的多角色工程团队技能包
- [[concepts-frameworks/agent-skills]] — Addy Osmani 出品的 20 工程技能库

## Sources

- [[Aegis-github]] — 官方 GitHub 仓库
- [[Aegis-readme]] — 官方 README（中文版本可用）
