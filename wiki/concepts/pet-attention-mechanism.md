---
title: "Pet Attention Mechanism"
type: concept
created: 2026-08-06
updated: 2026-08-06
sources:
  - https://mp.weixin.qq.com/s/Mx1pclSLzkRFXKEME24TYA
tags:
  - agent-ux
  - attention-management
  - peripheral-awareness
  - interrupt-routing
  - emotional-salience
  - desktop-agent
confidence: medium
---

# Pet Attention Mechanism

> 长任务改变了人/软件的时间关系：人可以离开工作台，只在决策、恢复、验收时回来。
> Pet 作为 Agent 桌面端的状态投影层，把计算机状态从「主动查询」转化为「周边视野中的被动感知」。

## 核心作用

Pet 在 vibe coding 场景中提供了情绪价值，但更深层的作用是 [lencx 2026]^[raw/articles/lencx-agent-dev-guide-20260806.md]：

> 把 Agent 状态从工作台内的主动查询，转化为周边视野中的被动感知。

三种需求压进一个轻量表面：

- **Peripheral awareness**（周边感知）— 免去持续轮询
- **Interrupt routing**（打断路由）— 把人叫回需要决定/恢复的任务
- **Emotional salience**（情绪显著性）— 提升关键通知的可感知性

## OpenAI Pets 设计原则 ^[ref 102-103^]

- 桌面浮动宠物定义为**跨应用跟随工作状态**的可选伴侣
- **选择宠物只改变外观，不改变 ChatGPT 完成任务的方式**
- 多个任务同时活跃时，Pet 按 `需要输入 / 阻塞 / 完成 / 运行` 的顺序争夺注意力
- 通知文档定位为**用户操作其他应用时的 chat 状态跟踪**

## 拟人化必须服从运行时真相

**Pet 深一层原则** ^[raw/articles/lencx-agent-dev-guide-20260806.md]：

- Pet 只读投影持久任务状态，能打开对应任务与证据
- `Blocked` 要如实呈现，动画只传达**注意力优先级**，结果由测试/Diff/trace/人工验收确认
- Codex TUI 的 `/pets` 与 `/pet` 表明同一机制可存在于终端；桌面形态额外提供跨窗口悬浮、跨任务排序、离开工作台后的持续可见性
- **无障碍、隐私、减少动画、通知节流都属于这套状态机**

## 与既有 Agent 状态流的关系

Pet 是状态机的**可视化投影层**，不是新的执行单元。其上游：

1. **持久任务状态** — Durable Agent 已在多 Wiki 页讨论
2. **状态类型分类** — Memory 五类（工作/事件/领域/情节/身份）
3. **注意力协议** — lencx 关注协议提出统一表达 `running / needs-input / blocked / ready`

下游：

- **CLI / GUI / Pet / 通知** 都应投影同一份运行时真相
- 通知节流 + 免打扰 + 隐私 + 人工接管入口是状态机的组成部分

## Hermes 实施映射

Hermes `petdex` skill 提供动画宠物伴侣 ^[Nick 部署环境]^[MEMORY.md]，符合本机制的可视化投影角色：

- 选择宠物仅改变外观，不影响 Agent 执行路径
- 动画状态（idle/working/blocked/needs-input）可作为注意力协议的具体输出
- Hermes 已支持 Cron 投递 → 多平台 → 状态通知 → 用户接管 的完整链路

## 实施清单（生产级 Pet 状态机）

```yaml
projections:
  terminal_TUI:
    - /pets list
    - /pet [name]
    - 状态符号 [running|blocked|needs-input|done]
  desktop_window:
    - 浮动窗口
    - 跨任务排序
    - 拖拽到工作台
  notification:
    - 系统通知
    - 托盘状态
    - 全局快捷键
  shared_truth_source:
    - 同一任务状态文件
    - 注意力协议 schema
    - 状态机驱动而不驱动状态机
```

## 引用

- OpenAI Pets: https://learn.chatgpt.com/docs/pets
- OpenAI Notifications: https://learn.chatgpt.com/docs/notifications
- Claude Code `/doctor` — 用于收缩过度膨胀的 Skills 与 CLAUDE.md（同 lencx 提到 ^[ref 6^]）
- Codex TUI: `/pets` 与 `/pet`

## 关联 Wiki 页

- [[Agent Native Stack]] — Pet 在第 5 节"入口分化，状态合流"中定位
- [[Harness-镜子-显形运动]] — "注意力协议" 与 "判断力光谱" 进一步发展
- lencx 文章全文：[summaries/harness-engineering/lencx-agent-dev-guide-20260806.md]
