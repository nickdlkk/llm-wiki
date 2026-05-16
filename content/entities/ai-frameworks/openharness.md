---
title: OpenHarness
type: entity
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/thu-nmrc/OpenHarness
tags:
  - AI-Agent
  - harness-engineering
  - autonomous
  - OpenClaw
  - Python
  - BSL-1.1
---

# OpenHarness

> Long-term, fully autonomous AI agent execution framework for OpenClaw built on the concept of Harness Engineering. Enables your AI to work tirelessly for you 24/7 with just a single command.

## 一句话定义

OpenHarness 是一个**基于 Harness Engineering 理念的长期自主 AI Agent 执行框架**，为 OpenClaw 构建，通过严格的状态持久化、断点恢复、外部验证机制，确保任务 24/7 持续正确执行，无需值守。

## 定位

```
Skills 定义 Agent 能做什么
Harness 定义 Agent 如何持续稳定地完成任务

OpenHarness = Harness Engineering 的生产实现
```

## 核心问题

大多数自主 AI 框架（如 AutoGPT、BabyAGI）依赖 Agent 的"主观判断"决定下一步。结果？
- 无限循环
- Context window 爆炸
- 过早任务完成

OpenHarness 的不同路径：**绝对可靠性 > 突发智能**

## 6 大支柱

| 支柱 | 框架实现 | 如何工作 |
|------|----------|----------|
| **1. 机器可验证合约** | `mission.md` + `eval_criteria.md` | 定义"什么算完成"的绝对机器可检查条件 |
| **2. 记录系统** | `playbook.md` + `progress.md` | 将执行步骤写入版本化文档，Agent 像工厂工人一样遵循 |
| **3. 感官和四肢** | `playbook.md` 中的工具定义 | 授予 AI 每个精确步骤所需的特定工具 |
| **4. 解决健忘症** | `heartbeat.md` + `progress.md` | 跨 Session 状态终极恢复，Agent 死亡后从精确行恢复 |
| **5. 外部验证** | `harness_eval.py` + `eval_criteria.md` | 独立验证脚本，AI 永远不能当自己的裁判 |
| **6. 熵控制** | `harness_cleanup.py` + Boundaries | 定期归档旧记录、压缩日志、清理临时文件防止 context window 崩溃 |

## 执行生命周期

```
┌─────────────────────────────────────┐
│  1. harness_boot.py → 检查状态      │
│  2. harness_heartbeat.py start     │
│  3. 读取 mission/heartbeat/playbook │
│  4. 从断点恢复 playbook 步骤        │
│  5. harness_heartbeat.py done/fail  │
│  6. harness_eval.py → 外部验证      │
│  7. harness_cleanup.py → 熵控制    │
│  8. 如果全完成 → mission_complete    │
└─────────────────────────────────────┘
```

## 三层自愈记忆

| 层级 | 文件 | 大小 | 说明 |
|------|------|------|------|
| **L1 (指针索引)** | `heartbeat.md` | < 2KB | 紧凑指针，始终最新 |
| **L2 (知识文件)** | `knowledge/*.md` | 按需 | 主题知识，按需加载 |
| **L3 (执行流)** | `logs/execution_stream.log` | 只追加 | 执行记录，仅 grep 访问 |

## 熔断器和卡住检测

`harness_boot.py` 现在包含熔断器，在 N 次连续失败后自动阻止执行（默认：3）。还检测"卡住"会话（状态保持 `running` 但前次运行崩溃），自动恢复到 `idle` 并增加失败计数。

## KAIROS 梦境模式

新 `harness_dream.py` 在空闲期间执行离线记忆整合。触发时，扫描执行流中的重复模式、整合碎片化知识、剪枝过时 L1 指针，将提炼的最佳实践注入 `playbook.md`。

## 多 Agent 协调器

新 `harness_coordinator.py` 通过文件系统 IPC 协议实现并行子任务执行。协调器通过向 worker `inbox/` 目录写入 XML 格式 Markdown 文件分发任务；worker 独立执行并将结果写入 `outbox/`；协调器收集、聚合、归档结果。

## 快速开始

```bash
# 1. 创建 harness 目录
mkdir -p ~/.openclaw/workspace/harness

# 2. 克隆 OpenHarness
git clone https://github.com/thu-nmrc/OpenHarness.git ~/.openclaw/workspace/harness
```

OpenClaw 会自动识别 `SKILL.md` 并为 Agent 配备 Harness 能力。

### 一句话触发

只需描述任务想法，框架自动完成：
1. 📂 创建工作目录
2. 📝 起草 `mission.md`（不可破坏的任务合约）
3. 🗺️ 制定 `playbook.md`（逐步执行指南）
4. ⚖️ 定义 `eval_criteria.md`（客观验证规则）
5. ⏱️ 配置 `cron_config.md`（24/7 调度规则）
6. ❤️ 初始化 `heartbeat.md` & `progress.md`（跨 Session 记忆）
7. 🚀 设置 cron job 并**立即启动第一次执行**

## 框架结构

```
harness/
├── SKILL.md                      ← Agent 技能入口
├── scripts/
│   ├── harness_boot.py           ← 引导：初始化 + 状态检查 + 熔断器
│   ├── harness_heartbeat.py      ← 心跳：状态读写 + 进度跟踪
│   ├── harness_memory.py         ← 三层记忆管理器
│   ├── harness_dream.py          ← KAIROS 梦境模式
│   ├── harness_coordinator.py    ← 多 Agent 协调器
│   ├── harness_eval.py           ← 外部验证
│   ├── harness_cleanup.py        ← 熵控制
│   ├── harness_setup_cron.py     ← 调度配置
│   └── harness_linter.py         ← 架构 lint
├── references/
│   ├── architecture.md           ← 架构说明
│   └── anti-patterns.md          ← 反模式列表
└── templates/
    ├── mission.md                ← 任务合约模板
    ├── playbook.md               ← 执行 playbook 模板
    ├── heartbeat.md              ← 心跳状态模板
    ├── progress.md               ← 进度日志模板
    ├── eval_criteria.md          ← 验证标准模板
    └── cron_config.md            ← 调度配置模板
```

## 对比其他框架

| 特性 | AutoGPT | BabyAGI | OpenHarness |
|------|---------|---------|-------------|
| **跨 Session 记忆** | ❌ 丢失上下文 | ❌ Vector DB 混乱 | ✅ `heartbeat.md` 精确断点恢复 |
| **完成验证** | ❌ AI 自我认证 | ❌ AI 自我认证 | ✅ 外部 `harness_eval.py` 严格审计 |
| **执行沙箱** | ❌ 无界 | ❌ 无界 | ✅ 严格 `progress.md`/`mission.md` 合约约束 |
| **熵控制** | ❌ Context 膨胀 | ❌ Context 膨胀 | ✅ 内置 `harness_cleanup.py` 垃圾回收 |
| **触发机制** | 手动启动 | 手动启动 | ✅ 24/7 Cron 调度，完全无人值守 |

## 技术栈

| 层次 | 技术 |
|------|------|
| 语言 | Python |
| 框架 | OpenClaw Plugin |
| 协议 | 文件系统 IPC、Cron |
| License | BSL 1.1 (学术/研究/非商业免费) |

## 路线图

- [ ] **E2E Playwright 集成**：真实浏览器验证
- [ ] **自动修复生成**：验证失败时自动生成修复建议
- [ ] **机械 Linter**：`harness_linter.py` 强制严格方向依赖
- [ ] **Git 自动提交和回滚**：Agent 破坏工作空间时自动回滚
- [ ] **自愈 PR**：Agent 遇到持续阻塞时写 PR 修复自己的工具

## 相关页面

- [[Harness Engineering]] — Agent 可靠工作工程化方法论
- [[agent-platforms/openclaw-managed-agents]] — OpenClaw 托管 Agent
