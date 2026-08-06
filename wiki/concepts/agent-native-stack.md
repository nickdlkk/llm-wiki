---
title: "Agent Native Stack"
type: concept
created: 2026-08-06
updated: 2026-08-06
sources:
  - https://mp.weixin.qq.com/s/Mx1pclSLzkRFXKEME24TYA
tags:
  - agent-infrastructure
  - architecture
  - harness-engineering
  - reasoning-core
  - execution-shell
confidence: high
---

# Agent Native Stack

> Agent 原生软件 = **概率性推理核心 + 确定性执行外壳**。
> 从 lencx 2026-07 长文蒸馏出的 11 节框架。原文精读：[summaries/harness-engineering/lencx-agent-dev-guide-20260806.md]

## 核心命题

模型从「回答问题」升级到「承担责任」时，最稀缺的不再是代码，而是 **可信完成**。要回答 7 个问题 ^[raw/articles/lencx-agent-dev-guide-20260806.md]：

1. 它以谁的身份行动？
2. 它只能触达什么？
3. 环境是否可复现？
4. 中断后能否恢复？
5. 结果如何验证？
6. 副作用如何审计？
7. 人类何时能够接管？

## 11 节框架（执行栈层次分解）

| 章节 | 层次 | 核心结论 |
|------|------|----------|
| 1. 回答不等于负责 | 状态模型 | 从 `prompt → response` 升级为 `observe → act → wait → verify → settle → pause/approve/retry/recover` |
| 2. Harness 持有全程 | 目标层 | Harness 持有目标，组织可恢复执行；Context/Loop/Graph/Skill 各司其职 |
| 3. 平台争夺 Agent 全栈 | 平台层 | Vercel 向下补执行，Cloudflare 向上补体验，二者收敛 |
| 4. 模型公司吞并数字工作 | 集成层 | Coding → Operating Layer 扩张；Bun/OpenClaw/AAIF 三种控制关系 |
| 5. 入口分化，状态合流 | 入口层 | CLI/Gateway/GUI/Pet/Browser 各有定位，状态共享 |
| 6. 浏览器成为能力平面 | 行动层 | 浏览器 = 传感器 + 执行器 + 身份容器 + 验证界面 + 本地计算 |
| 7. 环境决定能力边界 | 隔离层 | Electron vs Tauri 拓扑 + 完整环境闭包 |
| 8. 上层求快，底层求稳 | 语言层 | TS/Python 上、Rust/Go/Zig 下，分工而非替代 |
| 9. Skills 将知识变成操作 | 知识层 | 渐进披露 + 递归能力获取 + 通用 Skill 被模型吸收 |
| 10. 责任边界先标准化 | 治理层 | 10 条跨框架契约 |
| 11. 工程师转向结果责任 | 责任层 | 先定义完成，再设计恢复 |

## 关键判断（用于外部引用）

- **核心因果链**：生成成本下降 → 任务复杂度上升 → 稀缺性从代码转向可信结果
- **Monetization 原则**：开源交付边缘 SDK/协议，托管变现执行/身份/状态/浏览器/GPU/部署/可观测性
- **观察尺度**：高于 LangGraph 等具体框架，聚焦跨框架长期存在的 7 类系统责任
- **可信完成判定**：测试 + 可测指标 + Diff + 截图 + 外部状态 + 人工验收，而非模型自述
- **durable Agent 五问**：inbound/session/task 耐重启？子任务从语义 checkpoint 继续？final delivery 能对账？副作用补偿覆盖什么？approval 绑定原动作重启后仍有效？

## 跨章节模式：3 种趋同

文章尽管跨 11 节揭示了 3 个反复出现的趋同模式：

### 模式 1：分层裁剪 + 渐进披露

- **Claude Code** — 删 80% system prompt 后无测量损失
- **Skills** — 先目录 metadata → SKILL.md → references/scripts
- **Tool 设计** — `scriptc` 静态证明 + 显式拒绝无法降低的构造
- **Turbo** — 任务 DAG + content hash + 依赖显式声明

### 模式 2：状态分类型 + 差异化恢复

- **Memory 5 类**：工作状态/事件历史/领域知识/情节偏好/身份隐私
- **Cloudflare 3 类**：Agent state / Sandbox filesystem / Workflows
- **durable Agent 5 问**：重启 / checkpoint / 副作用补偿 / approval 绑定

### 模式 3：入口分工 + 状态共享

- **CLI / Gateway / GUI / Pet / Browser** — 不替代，分工
- **ChatGPT desktop** — 共享宿主，Chat/Work/Codex 三视图共存
- **Codex `/app` / Antigravity / Wave / WezTerm** — 同一 core 多 surface

## 与既有 Wiki 概念的关系

- [[Harness Engineering]] — 第二节"Prompt 管一次，Harness 管全程"是核心定义
- [[Memory-Systems]] — 第五类记忆分类（工作/事件/领域/情节/身份）
- [[Harness-Patterns/Dispatcher State Machine]] — 编排层 pattern
- [[OpenClaw]] — Gateway-first 路线
- [[Hermes Agent]] — Harness-first + 自进化路线

## 引用

- 原文 References (174 条)：`raw/articles/lencx-agent-dev-guide-20260806.md` 末尾
- 关键源：
  - OpenAI Harness Engineering: https://openai.com/index/harness-engineering/
  - Anthropic Managed Agents: https://www.anthropic.com/engineering/managed-agents
