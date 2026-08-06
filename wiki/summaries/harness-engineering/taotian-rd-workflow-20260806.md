---
title: "复杂业务团队的 AI Coding 交付实践：知识库、RD 流程和质量门禁"
summary: "淘天物流技术团队复杂业务 AI Coding 落地：知识库分层（main/applications/candidate/personal/template）+ RD 流程文件化（Markdown 落盘）/rd:* 命令 + 质量门禁前置（fail-fast）+ 95% 即可人工兜底；代码采纳率从 75% 提升到 95% 通过两轮迭代验证"
type: summary
source: "微信公众号"
url: https://mp.weixin.qq.com/s/aopO-3KO9lenKF5WHhBD7w
author: 寂秋
organization: 淘天集团-物流技术
date: 2026-08-06
created: 2026-08-06
updated: 2026-08-06
tags: [harness-engineering, ai-coding, knowledge-hierarchy, r-d-workflow, quality-gate, business-team, logi, taotian, 2026]
sources:
  - https://mp.weixin.qq.com/s/aopO-3KO9lenKF5WHhBD7w
confidence: high
---

# 复杂业务团队的 AI Coding 交付实践（蒸馏）

> **一句话总结**：复杂业务团队 AI Coding 的真正差距不在"是否用 Wiki + skills + 模板"，而在**知识库如何分层、RD 流程如何文件化、质量门禁如何前置**。代码采纳率通过两轮迭代从 75% 提升到 95%，关键是把业务约束前置到 PRD/requirement 阶段，而不是依赖编码阶段挽救。

## 核心命题

> **工具可以换，模型可以升级，底层的团队上下文和研发协议需要自己沉淀。**

**落地三阶段路径**：

```
知识库 + 工具链 → 自动化流程 → 自主协同交付
```

**第一阶段（本文重点）**：

- 知识库打底（沉淀团队上下文）
- Coding Agent CLI + skills + Markdown 模板
- 需求分析、应用拆解、实现校验、知识回补链路跑通

**RD（research development）**：作者内部简称，本质是一组 skills、命令协议和 md 文档。

> **不和某一个 Coding Agent 强绑定**。只要其他 Agent 能读取这些 Markdown 文件，理解 requirement/analysis/implementation-check/continue-prompt，就可以接着干活。

## 总体设计：三层资产

```
命令协议层   → .agents/commands/  + .agents/skills/
知识资产层   → knowledge/
RD 过程资产层 → rd/requirements/{requirementId}/
```

### RD 流程图

```
需求 / PRD / Bug / 变更
  → /rd:verify-prd         # 输入质量门禁
  → /rd:work               # 路由命令，自动引导下一步
  → /rd:clarify            # 需求澄清
  → /rd:analyze            # 需求分析 + 知识检索
  → /rd:decompose          # 拆应用级 requirement
  → /rd:verify-requirement # 编码前契约确认
  ↓
业务应用仓库开发
  → /rd:apply              # 按 requirement 实现
  → /rd:validate           # 与代码 diff 对账
  → /rd:code-review        # 业务需求 + 研发规范 CR
  → /rd:release-plan       # 发布计划 / 灰度回滚
  ↓
发布 → 知识回补
```

> 每个命令都是"研发过程里的检查点"，把原来散落在聊天记录、人脑和临时文档里的判断，落到可以复用、可以 review、可以接续的文件里。

`/rd:work` 是路由命令 — 根据用户输入和上下文自动推断意图，给出下一步引导。**事实上大家只需要输入 `/rd:work` 一个命令**。

### RD 适用边界

**适合**：

- 需求跨多个应用
- 业务状态和上下游协议复杂
- 历史兼容逻辑多
- 发布风险高
- 需要多轮澄清和拆解

**不适合（直接自然语言 + Agent 即可）**：

- 小局部修复
- 纯样式调整
- 一次性脚本
- 低风险工具需求

## 知识库设计：先分层，再进入研发流程

### 知识库目录结构

```
knowledge/
├── main/         # 业务域通用知识（跨应用）
├── applications/ # 应用范围知识
├── candidate/    # 候选知识暂存区
├── personal/     # 个人经验
├── template/     # 强约束写作模板
├── INDEX.md
├── README.md
├── KNOWLEDGE-RULES.md
└── ROUTING.md
```

### 5 层知识角色定位

| 层级 | 角色 | 内容 |
|------|------|------|
| **main/** | 业务领域"公共语境" | 跨应用术语、跨应用流程、通用状态、全局约束、多应用通用规则 |
| **applications/** | 应用知识（按应用拆） | 每个应用的产品主干 / 解决方案差异 / 基础索引 / 研发规范 |
| **candidate/** | 候选知识暂存区 | 待确认结论 + 来源 + 证据 + 可信度 + 待确认项 |
| **personal/** | 个人经验 | 排查经验、踩坑、判断方式、个人理解草稿 |
| **template/** | 强约束写作模板 | application / domain / flow / state / rule / code / tech 模板 |

### applications 内部分层

```
knowledge/applications/application-xxx/
├── application-xxx.md    # 应用总览（职责 / 边界 / 上下游 / 核心模块 / 入口）
├── INDEX.md              # 应用内导航（按关键词/业务身份/Topic/接口/状态/模型定位）
├── domain/
│   ├── product/          # 主干流程：创单、取消、下发、回告、状态流转、幂等、路由
│   ├── solution/         # 解决方案差异：业务身份/业务线/履约模式的差异化扩展
│   └── base/             # 基础索引：API、消息、模型、Repository、字段语义、调用关系
└── tech/                 # 研发规范、架构约束、MQ 处理、事务边界、常见踩坑
```

**AI 读应用知识路径**：

```
应用职责 → product 主干 → solution 差异 → base 接口/消息/模型/Repository
     → tech 研发规范 → 当前代码确认
```

### 核心原则

> **KB 提供稳定上下文，当前代码仍然是实现事实。**

接口签名、DTO 字段、Topic 配置、feature key、状态枚举、开关配置这类**变化快的内容**，知识库只提供定位入口。**真正改代码前，还是要回到当前仓库核对。**

### YAML Front Matter — 知识可信度元数据

```yaml
id: KB-APPLICATION-{DOMAIN}-{SEQ}
type: application
domain: {domain}
application: {appCode}
appType: 后端应用                 # 前端/后端
status: DRAFT                    # DRAFT/CANDIDATE/OFFICIAL/DEPRECATED
sourceType: official             # official/ai-assisted/personal
owner: {userId}
version: 1
updatedAt: YYYY-MM-DD HH:MM:SS
confidence: medium               # high/medium/low
stability: evolving              # stable/evolving/volatile
evidence:
  - code: {核心模块或仓库路径}
  - doc: {应用文档或系统说明}
  - human: {确认人/时间}
tags: [{tag1}, {tag2}]
anchors:                         # 跨文档检索锚点
  - APPLICATION:{appCode}
  - BIZ_IDENTITY:{identity1}
```

**YAML Front Matter 作用**：

- 让 AI 判断该知识是否可作为事实引用
- 证据来源（代码 / 文档 / 人工确认）
- 是否需要回代码核对
- 与哪些应用 / 状态 / 接口 / Topic / 模型 / 规则相关

> **复杂业务里，错误知识比没有知识更危险**。没有知识时，AI 可能会暴露不确定；错误知识被当成事实，就会直接把实现带偏。

### ROUTING：渐进式加载

`knowledge/ROUTING.md` 让 AI 收到需求后先抽取关键词、业务身份、应用名、Topic、接口、状态、模型、表名，然后定位：

```
候选业务域 → 候选应用 → 应用职责地图 → 知识入口 → 本地仓库路径
```

**3 种典型检索路径**：

| 关键词类型 | 检索路径 |
|-----------|---------|
| **Topic** | Topic → ROUTING 定位目标应用 → application INDEX → domain/base/msg.md → Producer/Consumer → 相关 flow → 代码确认 |
| **状态码** | state-*.md → 关联 flow-*.md → 关联 rule-*.md → 代码入口 → 当前仓库核对 |
| **业务身份** | main 领域定义 → 关联 applications → solution 目录 → 对照 product 主干 → 分析差异实现 |

> **复杂业务里的上下文不是塞得越多越好**。理想方式：**AI 在正确阶段，读取正确粒度的上下文**，只加载核心且对解决问题有用的那部分（渐进式加载）。

### 知识回补

```
personal 个人经验
  → candidate 候选知识
  → owner review
  → official 正式知识
  → 需求执行中被引用
  → 代码或业务变化后更新 / deprecated
```

**两条铁律**：

- 有价值的经验留在聊天记录里 → 下次没人找得到
- 未确认的推断直接进入正式 KB → 被 AI 当成事实引用

## RD 流程：用 Markdown 承载研发状态

### 需求目录结构

```
rd/requirements/{requirementId}/
├── source/
│   ├── input.md
│   ├── input.summary.md
│   ├── changes.md
│   ├── materials.yaml
│   └── materials/
├── clarification.md
├── execution-plan.md
├── analysis.md
├── analysis/
│   ├── application-a.md
│   └── application-b.md
├── decomposition.yaml
├── requirement-model.yaml
├── status.md
├── knowledge-backfill.md
└── applications/
    ├── application-a/
    │   ├── requirement.md         # 应用级开发契约
    │   ├── implementation-check.md
    │   └── continue-prompt.md
    └── application-b/
        └── requirement.md
```

**为什么全部落盘**：

1. **长会话可以拆开** — 大需求不需要一个会话从 PRD 写到代码
2. **新会话可以接上** — 上下文依赖落盘文件，不依赖聊天历史
3. **人可以 review** — 每个阶段关键判断在文件里，不藏在模型中间推理
4. **其他工具可以接入** — 同一个研发协议，不同 Coding Agent 都能读

### 1. 原始需求不能直接进入编码

**/rd:verify-prd 检查清单**（PRD 阶段）：

- 图片有没有文本化说明
- 状态码是否明确
- 上下游协议是否确认
- open item 是否阻塞开发
- 非目标是否写清楚
- 验收标准是否可验证
- 是否缺少关键字段说明
- 是否与知识库已有结论冲突

**/rd:verify-requirement 检查清单**（编码前）：

- 当前应用的目标是什么
- 非目标是什么
- 影响哪些接口、消息、状态、字段、规则
- 应该读哪些知识文件
- 应该看哪些代码入口
- 哪些校验要前置
- 哪些逻辑是异步
- 哪些能力要兼容历史
- 哪些问题仍然 blocked
- 验收标准是否可执行

**设计哲学：fail-fast**

> 能在 PRD 阶段暴露的问题，不拖到 requirement。能在 requirement 阶段暴露的问题，不拖到编码。能在方案阶段暴露的问题，不拖到联调。**越晚发现，返工成本越高。**

### 2. requirement.md 是应用级开发契约

**RD 流程最关键的产物**——每个应用自己的 requirement.md（by 应用的开发契约）。

合格 requirement.md 至少说清楚：

- 这个应用在本需求里的目标
- 非目标和不能碰的边界
- 相关知识依据和代码证据
- 涉及哪些接口、消息、状态、字段、规则
- 需要改哪些模块
- 编码前优先阅读哪些入口
- 需要满足哪些验收标准
- 还有哪些问题没确认
- 哪些稳定结论后续要回补知识库

> **跨应用需求，每个应用都有自己的 requirement**。编码会话不需要同时背着全部 PRD / 技术方案 / 应用上下文，只聚焦当前应用的开发契约。

### 3. validate 解决接续和对账

**/rd:validate** 拿应用级 requirement.md 与当前本地分支 diff 做对账：

| 状态 | 含义 |
|------|------|
| **done** | 已完成 |
| **partial** | 部分完成 |
| **todo** | 还没做 |
| **changed** | 需求/代码变化，需更新 requirement |
| **blocked** | 阻塞，需人工确认 |

**产出**：

- `implementation-check.md` — requirement 与代码实现对账结果
- `continue-prompt.md` — 用于新会话继续开发

> **开发可以中断，研发上下文不能丢。**

## 质量门禁：把人机 review 放在关键位置

### 9 个关键门禁

| 门禁 | 作用 |
|------|------|
| `/rd:verify-prd` | 输入质量门禁 |
| clarify review | 阻塞问题和业务口径确认 |
| analysis / routing review | 应用边界和影响范围确认 |
| `/rd:verify-requirement` | 编码前开发契约确认 |
| 方案 review | 扩展点、架构路径、兼容策略确认 |
| `/rd:validate` | requirement 与代码 diff 对账 |
| `/rd:code-review` | 发布前代码质量和方案一致性确认 |
| `/rd:release-plan` | 发布、灰度、回滚、观测确认 |
| knowledge backfill | 稳定经验回补知识库 |

### 人机 review 的真正职责

不是让人把 AI 做过的事情重做一遍，而是**在高价值位置介入，确认那些最容易造成返工和事故的业务事实**：

- 某个校验应该前置还是后置
- 某个状态从哪个 feature 里取
- 某个历史服务是否要兼容
- 某个异步事件是否允许真实调用外部服务
- 某个方案是否影响已有链路
- 某个字段是覆盖两份模型，还是只覆盖一份
- 某个开关默认值怎么设
- 发布时是否需要灰度和回滚

> **这些判断，往往比写代码本身更关键。**

**不追求 100% 全 AI**：

> AI 做到 95%，剩下只是简单改两行，研发同学手改更快，那就手改。**复杂业务交付的目标是总成本更低、质量更稳、风险更可控**，不是追求"代码全部由 AI 生成"的纯度。

## 案例：跨阶段真实交付（脱敏）

### 需求背景

上游服务商通过新回告状态触发"差异调整"，要求当前应用在主流程入口做前置校验，校验通过后落差异数据，以事件驱动异步调用既有订正流程。

### 第一轮：覆盖了大部分功能，但关键扩展点偏了

- 工具：同 Coding Agent CLI，最高能力档
- 需求拆解：领域拆分基本准确，**但漏掉了"重量大于 0 的校验"**
- 方案设计：AI 自己 review 出 7 个待确认点，人工澄清完成
- **编码结果：AI 代码采纳率约 75%**
- **最大问题**：PRD、澄清、设计过程均未明确需要在 xxx 中执行前置校验逻辑。**关键校验位置被放到相对靠后的位置，无法前置阻断服务商回传**

**根因**：第一轮 PRD/澄清/方案阶段没有把关键架构约束表达清楚：

```
新回告状态进入 → xxx 前置校验 → 校验通过后记录xx更新数据
  → 发出xx更新事件 → 异步调用 xxx → 复用订单订正逻辑
```

> 如果这个约束没有进入 requirement，AI 很容易理解"要做什么"，但把"在哪里做"理解错。

### 第二轮：把关键约束写进 requirement

**关键变化**：将"两阶段异步架构"明确写入 requirement + 方案输入。

**方案设计 prompt（关键约束）**：

> 基于提供的项目结构和需求文档，结合知识库仓库中相关业务的知识（先熟悉知识说明文件和索引文件，再按需读取具体知识文件），严格按照 requirement.md 中的技术要求和架构设计，实现本次差异调整需求。**要求遵循两阶段异步架构：第一阶段在 xxxProcess 中处理新回告状态并执行六项前置校验，第二阶段通过事件驱动方式在 xxxProcess 中完成计费重量订正**，并确保所有配置化参数、事件链路、数据模型变更都按需求规格正确实现。

**随后人工 review 历史业务身份 B、运单状态校验、运单完结时间等兼容细节**。

**第二轮结果**：

- 中断次数：3（问题澄清 1 次、Review 调整 2 次，部分依赖第一轮沉淀经验）
- **代码采纳率：95% 以上**
- 没有继续追求 100%，剩下简单手改更快

### 案例结论

> **第一轮不是 AI 完全做不了，而是关键业务约束没有在前置阶段被表达清楚。** 第二轮通过知识库、requirement 和 review 点，把关键约束放到了编码前，结果明显收敛。

### 比"代码采纳率"更重要的指标

| 指标 | 关注点 |
|------|--------|
| PRD 阶段发现 open item 数 | 输入质量 |
| requirement 阶段拦截不清晰项数 | 契约质量 |
| validate 阶段发现 requirement 与代码 diff 不一致数 | 实现保真度 |
| 代码采纳率 | 整体效率 |
| 人工中断发生阶段 | 流程瓶颈 |
| 问题归因分布（PRD 表达不清 / 知识库缺失 / 扩展点判断错误 / 纯编码） | 改进方向 |
| 需求结束后回补稳定知识数 | 知识资产增长 |

> **这些指标比"生成了多少行代码"更能说明 AI 研发流程有没有真正变好。**

## 为什么先打底，再自动化

> **自动化流程跑得越快，对底座的要求越高。** 如果知识库不准，requirement 不清楚，review 点没有设计好，自动化只会把错误更快地执行完。

**第一阶段三件事**：

1. **知识库要打磨** — 让团队经验从 owner 脑子里、历史 PRD 里、聊天记录里、线上问题里，逐步变成可检索、可路由、可验证的知识资产
2. **RD 流程要打磨** — 让需求输入、澄清、分析、拆解、实现校验和知识回补变成稳定协议
3. **质量门禁要打磨** — 让人机 review 点放在最关键的位置，尽早发现高返工成本问题

> **这个底座打稳以后，后面的自动化才有空间。**

## 走向 AI 研发 Harness

**Harness = 确定性研发交付环境**——把知识库、工具链、权限、流程状态、质量门禁、验证规则、发布约束统一收进去，让 Agent 在团队定义好的轨道里工作。

### 第一层：运行环境的确定性

Agent 进入一个需求时，应该知道：

- 本地应用仓库在哪里
- 使用哪些命令
- 先读哪些知识库入口
- requirement 写在哪里
- 实现校验怎么跑
- 哪些文件是权威上下文
- 哪些动作必须停下来等人确认

### 第二层：研发协议的确定性

Agent 不能拿到需求就直接写代码。应该沿着团队定义好的路径推进：

```
输入先 verify → 知识按 ROUTING 加载 → 需求过程分阶段落盘
  → 应用级 requirement 先确认 → 编码按契约执行
  → validate 对账 → 发布前 review → 稳定知识回补
```

**最终服务的不是某一次 AI 写代码写得多漂亮，而是团队交付能力的稳定提升**。

每做完一个需求，团队都能多沉淀一点：

- 更完整的业务上下文
- 更清楚的应用边界
- 更稳定的研发流程
- 更可复用的 requirement 模板
- 更明确的 review 点
- 更好的失败归因
- 更多可以回补知识库的经验

### 安全边界

- **只让 Agent 读取当前需求需要的仓库和知识目录**
- 敏感文档、线上数据、密钥、客户信息不进入 prompt
- 对外分享时，需求名称、内部链接、应用名、类名、配置 key 都需要脱敏

> **AI 研发流程越深入真实交付，权限和数据边界越要前置设计。**

## 写在最后

第一阶段五个明确判断：

1. **知识库要分层** — main/applications/candidate/personal/template
2. **RD 流程要文件化** — 底层产物是 Markdown，工具可换
3. **质量门禁要前置** — PRD/requirement/方案/validate/code-review/release-plan
4. **人机协同 review 是交付系统的一部分** — AI 负责分析实现，人负责关键判断
5. **不追求 100% 全 AI** — 价值在总交付成本、质量稳定性、风险可控性

> 这套东西看起来比"直接让 AI 写代码"慢一些。**但在复杂业务团队里，前面多花一点时间把知识、流程和质量门禁设计好，后面才能少返工、少走偏、少靠 owner 临时救火。**

## 与本 Wiki 既有页面的关联

| 主题 | 文章论断 | 关联 Wiki 页 |
|------|---------|-------------|
| Wiki + skills + 模板 共识 | 本文也确认 | [[LLM Wiki]] / [[Harness Engineering]] |
| 知识库分层 | main/applications/candidate/personal/template | [[LLM Wiki]] |
| 知识可信度元数据 | YAML Front Matter | [[Harness-Patterns/Dispatcher State Machine]] |
| ROUTING 渐进式加载 | 关键词定位 → 业务身份 → Topic → 状态码 | [[Harness-Engineering]] |
| RD 流程文件化 | Markdown 落盘 | [[Harness-Patterns/Dispatcher State Machine]] |
| `requirement.md` 是应用级契约 | by-application 开发契约 | [[Agent Native Stack]] |
| 9 个质量门禁 | 9 个 `verify-*` | [[Agent Native Stack]] |
| 数据飞轮 5 环节 | Bad Case / Good Case 喂养 | [[Eval Harness Production Agents 20260520]] |
| 95% 即可人工兜底 | 不追求 100% AI | [[Agent Native Stack]] |
| Harness 化 = 确定性研发环境 | 与 lencx 2026 的 Harness 论述一致 | [[Harness Engineering]] / [[Agent Native Stack]] |
| OpenClaw / Codex 切换 | Coding Agent 不强绑定 | [[OpenClaw]] / [[Hermes Agent]] |

## 引用

- 原文 References：见 `raw/articles/taotian-rd-workflow-20260806.md` 内容
- 团队：淘天集团-物流技术团队，专注于物流订单履约业务研发
