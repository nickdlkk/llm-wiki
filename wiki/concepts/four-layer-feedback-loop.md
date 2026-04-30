---
title: Four-Layer Feedback Loop
type: concept
created: 2026-04-16
updated: 2026-04-16
sources:
  - https://www.v2ex.com/t/1196036
tags:
  - feedback
  - testing
  - quality
  - CI
---

# Four-Layer Feedback Loop

> Agent 需要快速知道自己做错了什么。一层不够，四层刚好。

## 四层结构

```mermaid
graph TD
    L1["第一层: 编译<br/>Go/TS 即时类型检查<br/>反馈时间: <1s"]
    L2["第二层: 单测<br/>700+ 测试覆盖 domain/service 层<br/>反馈时间: ~5min"]
    L3["第三层: e2e<br/>端到端验证真实功能路径<br/>反馈时间: 更长"]
    L4["第四层: CI Pipeline<br/>PR 自动跑全量测试/lint/构建<br/>合并前的最后安全网"]

    L1 -->|"通过"| L2
    L2 -->|"通过"| L3
    L3 -->|"通过"| L4
    L4 -->|"通过"| Merge["合并到 main"]

    L1 -.->|"失败"| Agent
    L2 -.->|"失败"| Agent
    L3 -.->|"失败"| Agent
    L4 -.->|"失败"| Agent
```

| 层级 | 反馈时间 | 覆盖的错误类型 |
|---|---|---|
| 第一层：编译 | <1s | 语法错误、类型不匹配、API 签名错误 |
| 第二层：单测 | ~5min | 单元逻辑回归、多租户隔离等边界条件 |
| 第三层：e2e | 更长 | 多模块真实联动、集成边界 |
| 第四层：CI | PR 时 | 全部 + lint + type-check + 多平台构建 |

## 关键原则：反馈环路越短越精准

- **强类型作为编译期质量闸**：Agent 生成了签名不匹配的函数？编译失败。TypeScript 直接报错。这些错误在弱类型语言里会悄悄进入运行时。
- 单测覆盖 domain 和 service 层 — Agent 修改后 5 分钟内知道有没有引入回归
- e2e 验证真实的功能路径 — Agent 在单测里验证不到的集成边界

## 技术栈（AgentsMesh 案例）

- **Go** — backend，强类型
- **TypeScript** — frontend
- **Proto** — API 格式，跨语言类型检查
- **Air** — Go 热重载，修改 1 秒内重启

```mermaid
graph LR
    Agent["Agent 修改一行代码"] --> Compile["编译检查"]
    Compile -->|"通过"| UnitTest["单测 700+"]
    UnitTest -->|"通过"| E2E["e2e 测试"]
    E2E -->|"通过"| CI["CI Pipeline"]
    CI -->|"通过"| Merge["合并"]
```

## 参见

- [[Harness Engineering]] — 四层反馈闭环是 Harness 验证子系统的具体实现
- [[Three Engineering Primitives]] — 反馈闭环支撑分解和协调原语
