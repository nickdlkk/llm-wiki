---
title: "复杂业务团队的 AI Coding 交付实践：知识库、RD 流程和质量门禁"
author: 寂秋
organization: 淘天集团-物流技术
source_url: https://mp.weixin.qq.com/s/aopO-3KO9lenKF5WHhBD7w
date: 2026-08-06
ingested: 2026-08-06
sha256: b98f44a8dd2d5e276922767a3631e0db3fbabd53b879b0fad8bcfc4b866a1214
# Body 哈希（即以下 `---` 之后的内容）：原文结构化重写版
type: methodology-essay
tags: [knowledge-hierarchy, ai-coding, r-d-workflow, quality-gate, business-team, taotian, 2026]
---

> 微信公众号文章，作者为淘天集团物流技术团队寂秋。
> 全文核心：复杂业务下 AI Coding 交付的 Wiki + 技能 + 模板共识之下，真正的差距在分层、流程、门禁的具体设计。
> 编辑补充：原文大段段落已重组为结构化 Markdown，便于检索引用，但保留原文核心论断与案例数据。

复杂业务团队的 AI Coding 交付实践：知识库、RD 流程和质量门禁

## 整体背景

过去一段时间，复杂业务场景下的 AI 研发交付逐渐形成了一些共识：通过 Wiki 补齐团队上下文，通过 skills 和研发模板约束 AI 的分析、拆解和编码过程，再通过知识回补把需求里的经验沉淀下来。

真正值得花时间打磨的，是那些最有团队特征、最难被通用工具直接替代的东西：**业务知识、应用边界、研发规范、质量门禁、跨应用协作方式和历史经验**。工具可以换，模型可以升级，底层的团队上下文和研发协议需要自己沉淀。

落地分三阶段：

```
知识库 + 工具链 → 自动化流程 → 自主协同交付
```

第一阶段：建设知识库 + Coding Agent CLI + skills + Markdown 模板（本文重点）
第二阶段：把更多流程动作交给 Agent 自动推进
第三阶段：完整协同交付（开发、测试、发布、观测、回滚）

作者把内部这套流程称为 **RD（research development）**——本质是一组 skills、命令协议和 md 文档，不和某一个 Coding Agent 强绑定。

## 总体设计

### 三层资产

```
命令协议层   → .agents/commands/  + .agents/skills/
知识资产层   → knowledge/
RD 过程资产层 → rd/requirements/{requirementId}/
```

`/kb:*` 和 `/rd:*` 命令定义何时读取/停止/写盘。`knowledge/` 沉淀正式/候选/个人知识 + 模板 + 路由规则。`rd/requirements/{requirementId}/` 保存每个需求从输入 → 澄清 → 分析 → 拆解 → 实现校验 → 知识回补的全过程产物。

### RD 流程图

```
需求 / PRD / Bug / 变更
  → /rd:verify-prd        # 输入质量门禁
  → /rd:work              # 路由命令，自动引导下一步
  → /rd:clarify           # 需求澄清
  → /rd:analyze           # 需求分析 + 知识检索
  → /rd:decompose         # 拆应用级 requirement
  → /rd:verify-requirement # 编码前契约确认
  ↓
业务应用仓库开发
  → /rd:apply             # 按 requirement 实现
  → /rd:validate          # 与代码 diff 对账
  → /rd:code-review       # 业务需求 + 研发规范 CR
  → /rd:release-plan      # 发布计划 + 灰度回滚
  ↓
发布 → 知识回补
```

> 每个命令都是为了把"原来散落在聊天记录、人脑和临时文档里的判断"，落到可以复用、可以 review、可以接续的文件里。

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

### main：业务域通用知识

放跨应用、跨系统、跨业务线的通用知识：核心术语、跨应用流程、通用状态定义、全局技术约束、多应用都要遵守的业务规则。**main/ 负责帮 AI 建立大方向**，不解释某个应用内部怎么写代码。

### applications：应用范围知识

```
knowledge/applications/application-xxx/
├── application-xxx.md    # 应用总览（职责、边界、上下游）
├── INDEX.md              # 应用内导航
├── domain/
│   ├── product/          # 主干流程（创单、取消、下发、回告、状态流转）
│   ├── solution/         # 解决方案（按业务身份/业务线差异）
│   └── base/             # 基础索引（API、消息、模型、Repository）
└── tech/                 # 研发规范、架构约束、MQ 处理、事务边界
```

**AI 读应用知识的路径**：先看应用职责 → 再看 product 主干能力 → 再看 solution 差异 → 再看 base 索引 → 必要时读 tech 规范 → 最后回到当前代码确认。

**核心原则**：**KB 提供稳定上下文，当前代码仍然是实现事实**。接口签名、DTO 字段、Topic 配置、feature key、状态枚举、开关配置等变化快的内容，KB 只提供定位入口，最终回到仓库核对。

### candidate：候选知识暂存区

避免两个极端：
- 有价值的经验留在聊天记录里，下次没人找得到
- 未确认的推断直接进入正式 KB，后面被 AI 当成事实引用

`candidate/` 承接待确认知识，标清来源、证据、可信度、待确认项，review 后才能合并到 main/ 或 applications/。

### personal：个人研发经验

包括线上问题排查、模块踩坑、异常日志判断、历史坑背景、个人理解草稿。**如果某条 personal 经验被多次验证或被 owner 确认，可转 candidate，再进入正式知识库。**

### template：强约束的知识写作模板

模板不是建议格式，而是强约束。AI 写知识时最容易出现"粒度漂移"，模板把结构固定下来。

#### application 模板示例（YAML Front Matter + 标准章节）

```yaml
# 必填字段
id: KB-APPLICATION-{DOMAIN}-{SEQ}      # 知识唯一编号
type: application                      # 知识对象类型
domain: {domain}                       # 业务域
application: {appCode}                 # 应用编码
appType: 后端应用                       # 前端应用 / 后端应用
status: DRAFT                          # DRAFT/CANDIDATE/OFFICIAL/DEPRECATED
sourceType: official                   # official/ai-assisted/personal
owner: {userId}
version: 1
updatedAt: YYYY-MM-DD HH:MM:SS
confidence: medium                     # high/medium/low
stability: evolving                    # stable/evolving/volatile
evidence:
  - code: {核心模块或仓库路径}
  - doc: {应用文档或系统说明}
  - human: {确认人/时间}
tags: [{tag1}, {tag2}]
anchors:                               # 给 AI 用于跨文档检索
  - APPLICATION:{appCode}
  - BIZ_IDENTITY:{identity1}
```

**YAML Front Matter 字段作用**：让 AI 判断该知识是否可作为事实引用、证据来源、是否需回代码核对、和哪些应用/状态/接口/Topic/模型/规则相关。

> **复杂业务里，错误知识比没有知识更危险**。没有知识时，AI 可能会暴露不确定；错误知识被当成事实，就会直接把实现带偏。

### ROUTING：让 AI 先定位，再读取

`knowledge/ROUTING.md` 让 AI 收到需求后先抽取关键词、业务身份、应用名、Topic、接口、状态、模型、表名，然后定位：

```
候选业务域 → 候选应用 → 应用职责地图 → 知识入口 → 本地仓库路径
```

**Topic 检索路径**：Topic → ROUTING 定位目标应用 → 读取 application INDEX → 读取 domain/base/msg.md → 找到 Producer/Consumer → 进入相关 product flow 或 solution flow → 回到代码确认消费入口。

**状态码检索路径**：状态码 → 查 state-*.md → 查关联 flow-*.md → 查相关 rule-*.md → 定位代码入口 → 回到当前仓库核对实现。

**业务身份检索路径**：业务身份 → 进入 main 领域定义 → 定位相关 applications → 读取对应 solution → 对照 product 主干 → 分析差异实现。

**解决上下文窗口占用问题**：复杂业务的上下文不是塞得越多越好。理想方式是 **AI 在正确阶段，读取正确粒度的上下文**，只加载核心且对解决问题有用的那部分，避免被不重要的信息影响导致实现跑偏（渐进式加载）。

### 知识回补

```
personal 个人经验
  → candidate 候选知识
  → owner review
  → official 正式知识
  → 需求执行中被引用
  → 代码或业务变化后更新 / deprecated
```

接口签名、Topic、字段、枚举、开关等变化快的信息，KB 只提供定位入口，最终仍要回到代码确认。

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

**关键设计**：所有关键过程全部落盘到 Markdown 和 YAML，不依赖聊天历史。

**好处**：
1. **长会话可以拆开** — 大需求不需要在一个会话里从 PRD 一路写到代码
2. **新会话可以接上** — 上下文不依赖聊天历史，而是依赖落盘文件
3. **人可以 review** — 每个阶段的关键判断都在文件里，不会藏在模型的中间推理里
4. **其他工具可以接入** — 同一个研发协议，不同 Coding Agent 都能读

### 1. 原始需求不能直接进入编码

**/rd:verify-prd** 检查 PRD 是否有明显缺口：
- 图片有没有文本化说明
- 状态码是否明确
- 上下游协议是否确认
- open item 是否会阻塞开发
- 非目标是否写清楚
- 验收标准是否可验证
- 是否缺少关键字段说明
- 是否存在和知识库已有结论冲突的地方

**/rd:verify-requirement** 编码前再确认：
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

**设计哲学：fail-fast** — 能在 PRD 阶段暴露的问题，不拖到 requirement；能在 requirement 阶段暴露的问题，不拖到编码；能在方案阶段暴露的问题，不拖到联调。**越晚发现，返工成本越高**。

### 2. requirement.md 是应用级开发契约

**RD 流程里最关键的产物，是每个应用自己的 requirement.md**（by 应用的开发契约）。

一份合格的 requirement.md 至少说清楚：
- 这个应用在本需求里的目标
- 非目标和不能碰的边界
- 相关知识依据和代码证据
- 涉及哪些接口、消息、状态、字段和规则
- 需要改哪些模块
- 编码前优先阅读哪些入口
- 需要满足哪些验收标准
- 还有哪些问题没确认
- 哪些稳定结论后续要回补知识库

> 对于跨应用需求，每个应用都有自己的 requirement。这样编码会话不需要同时背着全部 PRD、全部技术方案、全部应用上下文，只聚焦当前应用的开发契约。

### 3. validate 解决接续和对账问题

**/rd:validate** 拿应用级 requirement.md 与当前本地分支 diff 做对账，判断每个需求项状态：

- **done**：已经完成
- **partial**：部分完成
- **todo**：还没做
- **changed**：需求或代码发生变化，需要更新 requirement
- **blocked**：存在阻塞，需要人工确认

validate 产出：
- `implementation-check.md` — requirement 与代码实现的对账结果
- `continue-prompt.md` — 用于新会话继续开发

> **开发可以中断，研发上下文不能丢**。

## 质量门禁：把人机 review 放在关键位置

**最核心的两个点是知识库和质量门禁**。知识库决定 AI 能不能理解业务。质量门禁决定 AI 能不能进入真实交付。

### 重要门禁清单

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

> **这些判断，往往比写代码本身更关键**。

**不追求 100% 全 AI**：AI 做到 95%，剩下手改更快就手改。复杂业务交付的目标是总成本更低、质量更稳、风险更可控，不是追求"代码全部由 AI 生成"的纯度。

## 案例：跨阶段真实交付（脱敏）

### 需求背景

上游服务商通过一个新的回告状态触发"差异调整"，要求当前应用在主流程入口处做一系列前置校验，校验通过后落差异数据，并以事件驱动方式异步调用既有的订正流程。

### 第一轮：覆盖了大部分功能，但关键扩展点偏了

- 工具：同 Coding Agent CLI，最高能力档
- 需求拆解：领域拆分基本准确，需求文档也生成了，**但漏掉了"重量大于 0 的校验"**
- 方案设计：AI 自己 review 出 7 个待确认点，人工澄清完成
- 编码结果：**AI 代码采纳率约 75%**
- **最大问题**：PRD、澄清及设计过程均未明确需要在 xxx 中执行前置校验逻辑。关键校验位置被放到相对靠后的位置，**无法前置阻断服务商回传**

这说明前面 PRD、澄清和方案阶段没有把关键架构约束表达清楚：
```
新回告状态进入 → xxx 前置校验 → 校验通过后记录xx更新数据
              → 发出xx更新事件 → 异步调用 xxx → 复用订单订正逻辑
```

> 如果这个约束没有进入 requirement，AI 很容易理解"要做什么"，但把"在哪里做"理解错。

### 第二轮：把关键约束写进 requirement

第二轮关键变化：把"两阶段异步架构"明确写进 requirement 和方案输入里。

**方案设计 prompt（关键约束）**：
> 基于提供的项目结构和需求文档，结合知识库仓库中相关业务的知识（先熟悉知识说明文件和索引文件，再按需读取具体知识文件），严格按照 requirement.md 中的技术要求和架构设计，实现本次差异调整需求。**要求遵循两阶段异步架构：第一阶段在 xxxProcess 中处理新回告状态并执行六项前置校验，第二阶段通过事件驱动方式在 xxxProcess 中完成计费重量订正**。

随后继续人工 review 历史业务身份 B、运单状态校验、运单完结时间等兼容细节。

**第二轮结果**：
- 中断次数：3（问题澄清 1 次、Review 调整 2 次）
- 代码采纳率：**95% 以上**
- 没有继续追求 100%，剩下简单手改更快

**结论**：第一轮不是 AI 完全做不了，而是**关键业务约束没有在前置阶段被表达清楚**。第二轮通过知识库、requirement 和 review 点，把关键约束放到了编码前，结果明显收敛。

### 比"代码采纳率"更重要的指标

> 我们没有只看"AI 写了多少代码"，而是更关注几个更贴近交付质量的指标：

- PRD 阶段发现了多少 open item
- requirement 阶段拦截了多少不清晰项
- validate 阶段发现了多少 requirement 与代码 diff 不一致
- 代码采纳率大概是多少
- 人工中断发生在哪些阶段
- 问题是 PRD 表达不清、知识库缺失、扩展点判断错误，还是纯编码问题
- 需求结束后回补了多少稳定知识

> **这些指标比"生成了多少行代码"更能说明 AI 研发流程有没有真正变好。**

## 为什么先打底再自动化

> 自动化流程跑得越快，对底座的要求越高。**如果知识库不准，requirement 不清楚，review 点没有设计好，自动化只会把错误更快地执行完**。

第一阶段三件事：
1. **知识库要打磨** — 让团队经验从 owner 脑子里、历史 PRD 里、聊天记录里、线上问题里，逐步变成可检索、可路由、可验证的知识资产
2. **RD 流程要打磨** — 让需求输入、澄清、分析、拆解、实现校验和知识回补变成稳定协议
3. **质量门禁要打磨** — 让人机 review 点放在最关键的位置，尽早发现高返工成本问题

> **这个底座打稳以后，后面的自动化才有空间**。

## 走向 AI 研发 Harness

把实践往 Harness 化推进。**Harness = 确定性研发交付环境**——把知识库、工具链、权限、流程状态、质量门禁、验证规则和发布约束统一收进去，让 Agent 在团队定义好的轨道里工作。

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

> **AI 研发流程越深入真实交付，权限和数据边界越要前置设计**。

## 写在最后

第一阶段已经明确的几个判断：

1. **知识库要分层** — main/applications/candidate/personal/template
2. **RD 流程要文件化** — 底层产物是 Markdown，工具可换
3. **质量门禁要前置** — PRD/requirement/方案/validate/code-review/release-plan
4. **人机协同 review 是交付系统的一部分** — AI 负责分析和实现，人负责关键判断
5. **不追求 100% 全 AI** — 价值在总交付成本、质量稳定性、风险可控性

> 这套东西看起来比"直接让 AI 写代码"慢一些。但在复杂业务团队里，前面多花一点时间把知识、流程和质量门禁设计好，后面才能少返工、少走偏、少靠 owner 临时救火。

## 团队介绍

本文作者寂秋，来自淘天集团-物流技术团队。专注于物流订单履约业务研发的技术团队。
