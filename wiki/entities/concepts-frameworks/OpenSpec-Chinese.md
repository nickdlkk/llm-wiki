---
title: OpenSpec-Chinese
type: entity
created: 2026-04-28
updated: 2026-04-28
sources:
  - https://github.com/OpenLoaf/OpenSpec-Chinese
tags:
  - SDD
  - openspec
  - chinese-localization
  - specification
  - AI-coding
---

# OpenSpec-Chinese

**OpenLoaf/OpenSpec-Chinese** — OpenSpec 的中文本地化版本，为 AI 编程助手提供规范驱动的开发流程，完整中文界面支持。MIT License。

## 核心定位

基于 [org-hex/openspec-chinese](https://github.com/org-hex/openspec-chinese) 的中文本地化版本。OpenSpec 本身是一个 AI 驱动的系统化开发规范管理框架——在写代码前先与 AI 达成规范共识。

## 关键特性

- **中文界面支持** — 完整中文 UI 和文档
- **零侵入性设计** — 原有代码一行不改，完全独立实现
- **规范驱动开发** — 在编写代码前与 AI 达成规范共识
- **支持 20+ AI 工具** — Claude Code、Cursor、Cline 等
- **无需 API 密钥** — 本地运行

## 与原版 OpenSpec 对比

| 特性 | 原版 OpenSpec | OpenSpec-Chinese |
|------|-------------|-----------------|
| 界面语言 | 英文 | 中文 |
| CLI 命令 | `openspec` | `openspec-chinese` |
| 原始代码修改 | — | 零修改（完全兼容） |
| 中文模板 | — | ✅ 完整支持 |
| Gherkin 关键字 | 英文 | 英文（保持兼容性） |

## 技术栈

- **Node.js >= 20.19.0**
- npm 包：`@org-hex/openspec-chinese`
- TypeScript + Vitest 测试
- 内置 ESLint + Nix flake 支持

## 目录结构

- `openspec/` — OpenSpec 核心源码
- `src/` — TypeScript 源码
- `bin/` — CLI 入口
- `schemas/` — JSON Schema 定义
- `docs/` — 文档
