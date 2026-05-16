---
title: Dev vs Deploy Integration
type: concept
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/glitternetwork/pinme
  - https://github.com/DioxusLabs/dioxus
  - https://github.com/t3-oss/create-t3-app
  - https://github.com/Marve10s/Better-Fullstack
  - https://github.com/stacksjs/stacks
  - https://github.com/tauri-apps/tauri
tags:
  - framework-comparison
  - fullstack
  - developer-experience
  - deployment
---

# Dev vs Deploy Integration

## 核心区分

| 维度 | 开发一体化 (Dev Integration) | 部署一体化 (Deploy Integration) |
|---|---|---|
| **解决的问题** | 从零到可运行的内循环 | 从代码到上线的外循环 |
| **核心价值** | 脚手架 + 热重载 + 工具链内嵌 | 一键构建 + 发布到目标平台 |
| **用户阶段** | 写代码阶段 | 交付阶段 |
| **代表项目** | Dioxus, create-t3-app, Better-Fullstack | pinme |
| **是否包含运行时** | 内嵌完整运行时 | 只负责打包和发布 |

**开发一体化**：覆盖编码 → 调试 → 内测的内循环，提供脚手架、热重载、内置工具链，让开发者在同个工具内完成全流程。

**部署一体化**：覆盖构建 → 发布 → 分发的外循环，提供一键部署到指定平台（IPFS、Cloudflare Workers、Serverless），开发者写完代码后一条命令上线。

## 代表项目对比

### pinme — 部署一体化

**定位**：零配置 CLI，一条命令部署前端/全栈到 IPFS + Cloudflare Workers/D1。

```
pinme create my-project   # 创建 Cloudflare Worker + D1 项目
pinme save                # 一条命令部署全部（Worker + SQL + 前端）
```

| 维度 | 详情 |
|---|---|
| **语言** | TypeScript |
| **核心能力** | `pinme create` 初始化项目模板，`pinme save` 全量部署，`pinme upload` 静态资源上传 IPFS |
| **技术栈** | Cloudflare Workers (Edge Runtime) + D1 (SQLite at edge) + IPFS |
| **差异化** | AI Agent 原生友好（Guardrails + Claude Code Skill），去中心化 IPFS 托管 |
| **Stars** | ~3.3k |
| **定位** | 专注「部署」后半段，不涉及开发体验 |

### Dioxus — 开发一体化

**定位**：Rust 全栈 UI 框架，一套代码跑 Web / 桌面 / 移动 / 服务端，零配置 + 内置热重载。

```rust
fn app() -> Element {
    let mut count = use_signal(|| 0);
    rsx! {
        h1 { "High-Five: {count}" }
        button { onclick: move |_| count += 1, "Up!" }
        button { onclick: move |_| count -= 1, "Down!" }
    }
}
```

| 维度 | 详情 |
|---|---|
| **语言** | Rust |
| **核心能力** | 零配置初始化、内置热重载、signals 状态管理、Server Functions (全栈)、跨平台 bundler |
| **技术栈** | Rust + WASM，Web/Desktop(Tauri)/Mobile/TUI 多渲染后端 |
| **差异化** | 类 React 语法但编译到 WASM，性能极高；一条 Rust 代码多端运行 |
| **Stars** | ~24.5k |
| **定位** | 专注「开发」前端体验，部署是 CLI bundler 的副产品 |

### create-t3-app — 开发一体化（脚手架型）

**定位**：T3 Stack 官方 CLI，交互式生成 typesafe 全栈 Next.js 项目，按需组合模块。

```bash
npm create t3-app@latest   # 交互式选择：tRPC / Prisma / NextAuth / Tailwind
```

| 维度 | 详情 |
|---|---|
| **语言** | TypeScript |
| **核心能力** | 交互式脚手架、模块化按需组合、全栈类型安全 |
| **技术栈** | Next.js + tRPC + Prisma + NextAuth + Tailwind CSS |
| **差异化** | 每个模块独立可选，不过度设计；类型安全是核心约束 |
| **Stars** | ~22k |
| **定位** | 生成项目结构，不内置部署流程（交给 Vercel/Railway 等） |

### Better-Fullstack — 开发一体化（脚手架型）

**定位**：从 425 种技术栈中任选，一条命令生成前后端并自动连接。

| 维度 | 详情 |
|---|---|
| **语言** | 多语言 |
| **核心能力** | 425 种技术栈组合、自动化 wiring |
| **差异化** | 选择面最广，不绑定特定框架 |
| **定位** | 专注脚手架生成，不含热重载/工具链 |

### Stacks.js — 开发一体化（全栈型）

**定位**：现代全栈框架，前端 + 后端 + 云，一站式开发。

| 维度 | 详情 |
|---|---|
| **语言** | TypeScript |
| **核心能力** | 前端+后端+云一体化开发 |
| **差异化** | 覆盖开发到云端，但不专注部署 |
| **定位** | 全栈开发框架，类似Remix/Next.js但更一体化 |

### Tauri — 开发一体化（桌面专用）

**定位**：Rust 后端 + Web 前端的桌面/移动 App 框架，内置热重载，比 Electron 轻量。

| 维度 | 详情 |
|---|---|
| **语言** | Rust (backend) + Web (frontend) |
| **核心能力** | 桌面/移动 App 热重载、原生系统集成、IPC 通信 |
| **差异化** | 不做 Web 部署，专注打包成原生 App |
| **Stars** | ~28k+ |
| **定位** | 开发原生桌面 App，部署目标是应用商店/可执行文件 |

## 横向对比矩阵

| 项目 | 类型 | 语言 | 热重载 | 脚手架 | 跨平台 | 内置部署 | 目标用户 |
|---|---|---|---|---|---|---|---|
| **pinme** | 部署 | TypeScript | ❌ | ✅ create | ❌ | ✅ IPFS/Workers | 想要快速上线的前端开发者 |
| **Dioxus** | 开发 | Rust | ✅ | ✅ init | ✅ Web/Desktop/Mobile/TUI | ❌ | 需要高性能跨平台 UI 的开发者 |
| **create-t3-app** | 开发 | TypeScript | ❌ | ✅ interactive | ❌ | ❌ | 想要 typesafe Next.js 项目的开发者 |
| **Better-Fullstack** | 开发 | 多语言 | ❌ | ✅ 425 stacks | ❌ | ❌ | 想要任意技术栈组合的开发者 |
| **Stacks.js** | 开发 | TypeScript | ✅ | ✅ | ❌ | ❌ | 想要前端+后端+云一体的开发者 |
| **Tauri** | 开发 | Rust+Web | ✅ | ✅ | ✅ Desktop/Mobile | ❌ | 想要原生桌面 App 的开发者 |

## 关键洞察

### 1. 两者是互补关系，非竞争关系

开发者通常先用 **开发一体化** 工具写代码（Dioxus / create-t3-app），再用 **部署一体化** 工具发布（pinme / Vercel / Cloudflare Pages）。pinme 的差异化在于专注 IPFS 去中心化部署，而非替代开发框架。

### 2. 开发一体化 = 脚手架 + 运行时 + 工具链

成功的开发一体化框架具备三要素：
- **脚手架**：零配置或交互式生成项目结构
- **运行时**：内置开发服务器、热重载、状态管理
- **工具链**：构建、打包、发布的 CLI 内嵌

Dioxus 三者齐全；create-t3-app 有脚手架和类型安全，但热重载和运行时依赖 Next.js。

### 3. pinme 的差异化在于 AI Agent 原生支持

pinme 是唯一一个内置 **Guardrails**（防止 AI Agent 执行危险操作）和 **Claude Code Skill** 的部署工具。这使其在 AI Coding Agent 工作流中有独特价值——Agent 写完代码后可以直接调用 `pinme save` 发布，无需人工介入。

### 4. Rust 框架（Dioxus/Tauri）在性能和体验上领先

Rust 编译到 WASM 或原生代码，性能远高于 JS 运行时。Dioxus 的 `sledgehammer` 集成 bundler 和 Tauri 的 Rust 后端都是各自领域的性能标杆。

## 选型决策树

```
是否需要写代码？
├── 否 → 纯部署 → pinme（IPFS）/ Vercel（Web）
└── 是
    ├── 需要跨平台（Web+桌面+移动） → Dioxus
    ├── 只需要 Web + TypeScript 安全 → create-t3-app
    ├── 想要任意技术栈组合 → Better-Fullstack
    └── 想要桌面原生 App → Tauri
```

## 相关页面

- [[pinme]] — 部署一体化代表
- [[Dioxus]] — 开发一体化代表（跨平台 UI）
- [[create-t3-app]] — 开发一体化代表（TypeScript 全栈脚手架）
