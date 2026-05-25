---
title: QQ音乐Harness Engineering实践
type: summary
created: 2026-05-25
updated: 2026-05-25
sources:
  - https://mp.weixin.qq.com/s/yw3DvqKBIV5fIZkSG12zdA
tags:
  - harness-engineering
  - qq音乐
  - multi-agent
  - context-engineering
  - self-refinement
---

> 📄 **原文**：[《QQ音乐Harness Engineering实践》](https://mp.weixin.qq.com/s/yw3DvqKBIV5fIZkSG12zdA)

# QQ音乐Harness Engineering实践

## 一句话总结

QQ音乐基于 50+ 微服务的单仓多服务场景，自研了覆盖 **五阶段 + 四门禁 + 三层知识 + 三仓联动** 的 Harness Engineering 框架，补齐 L5 工程治理层，复用 Claude Code / Codex CLI 等执行层工具，让 AI 在"被约束的上下文"里高效生成。

## 核心公式

$$\text{代码产出} = \text{AI能力} \times \text{上下文质量}$$

**乘号而非加号**：上下文质量趋近零时，模型再强产出也是零。提升上下文质量比提升模型能力更高效——前者掌握在团队自己手中，后者依赖外部厂商。

## Vibe Coding 的三大结构性缺陷

| 维度 | Vibe Coding 典型表现 | 生产级工程要求 |
|------|---------------------|--------------|
| 信息损耗 | 同一句话多次执行给出不同实现，AI 按自己理解"猜"需求 | 需求→设计→代码每步有显式产出和可追溯关系 |
| 知识孤岛 | AI 只知通用知识，不懂团队历史决策和私有约束 | 团队知识需持久化为 AI 可消费的工程制品 |
| 验证断档 | "能跑"就直接提交，概率性错误滑进主干 | 每个关键节点有可机读的质量门禁和审计记录 |

## 五阶段 + 四门禁

让错误死在代价最低的地方。

```
阶段1 初始化 → 阶段2 需求定义 ⭐ → 阶段3 设计 ⭐ → 阶段4 开发 ⭐⭐ → 阶段5 交付
                                        门禁位置在阶段2.2、3.3、4.2、4.3
```

**四道强制门禁**：

| 门禁 | 位置 | 阻塞条件 |
|------|------|---------|
| 需求评审门禁 | 阶段 2.2 | 需求文档不合格 / 评审未通过 |
| 设计门禁 | 阶段 3.3 | 设计评审未通过 / 追溯链不达标 |
| Dev 进入门禁 | 阶段 4.2 | `tasks/features.json` 缺失或不合法 |
| 服务仓库检查门禁 | 阶段 4.3 | 三仓分支不一致 / 服务仓库未就位 |

门禁口径收拢在 `context/harness-framework/main-process-numbering.md`——这是流程语义的**唯一真相源**。每个门禁都有对应的 Agent/Skill + markdown 检查规范，结论写入文件（机读，非口头）。

## 三层知识体系

AI 按"团队 → 项目 → 模块 → 服务"逐层缩小范围，O(1) 命中。

| 层级 | 位置 | 典型内容 |
|------|------|---------|
| 团队级 | `context/team/` | Git 规范、错误码空间、日志规范 |
| 框架工程级 | `context/harness-framework/` | 五阶段流程、门禁规则、文档模板 |
| 服务级 | `context/project/{project}/{module}/` | 架构图、API、踩坑经验、SOP |

## 三仓联动

每个需求在三个仓库里使用**完全相同的分支名** `feature/{devops-name}/{tapd-id}`：

- **Harness 仓（脑）**：规范、知识、需求状态、工具链
- **业务仓（手脚）**：代码和测试，路径由 `.service-matrix/dependencies.yaml` 声明
- **IDL 契约仓（神经）**：跨服务协议（.jce），路径由同一文件派生

阶段 4.3 服务仓库检查门禁强制校验三仓分支一致性，不一致直接阻塞进入 4.4 编码循环。

## Service Matrix 单一真相源

`.service-matrix/dependencies.yaml` 是服务拓扑和仓库路径的单一真相源：

- 路径从不硬编码，用 `{business-repo}` / `{idl-repo}` 占位符，跨机器无缝迁移
- 目前管理 **57 个服务**，路径深度分布（21个1级、32个2级、4个3级）——框架对深度不作强假设
- CI 跑 `scripts/validate-service-matrix.js` 保证占位符正确解析

## Skill Agent Command 三件套

34 个 Skill + 24 个 Agent + 35 个 Slash Command，全部是版本化 markdown 文件，可 code review、diff、rollback——Knowledge as Code 的物理实现。

**阶段 4.4 的代码审查被拆成 8 个维度的独立 Agent 并行执行**：

```
code-review-preparer Agent（收集 diff + 上下文）
  → 分发给 8 个专家 Agent 并行：
    设计一致性 | 复杂度 | 并发安全 | 错误处理 | 安全漏洞 | 契约一致 | 追溯性 | 辅助检查
  → code-review-report Skill 聚合结论写入 reviews/*.md
```

## Self-Refinement 闭环

让每次纠正都成为团队资产：

1. 用户纠正 AI 某个错误
2. AI 识别是"模式性教训"还是"一次性 diff"
3. 若是模式性 → 主动提议沉淀层级（团队级 / 框架工程级 / 服务级）
4. 用户确认 → 生成 `experience/*.md` 或更新 Skill / 修订规范
5. 下次同类场景，AI 主动引用

## L5 工程治理层定位

Harness Engineering **不替代** Cursor / Claude Code / Codex CLI，而是补齐它们上方的治理层协议：

```
L5 治理层（自研）
  - 五阶段流程 + 四道门禁 + 三层知识体系 + 服务矩阵 + Self-Refinement

L3/L4 执行层（复用开源）
  - Claude Code / Codex CLI / Gemini CLI / Continue

L1/L2 体验层（第三方）
  - IDE、补全、对话、diff 可视化
```

## 技术路线：把业务约束编码成工程制品

| 工程制品 | 作用 |
|---------|------|
| `AGENTS.md` | 全局协作规范和硬规则入口 |
| `.codebuddy/skills/` | 可复用能力单元 |
| `.codebuddy/agents/` | 专家角色定义 |
| `.codebuddy/commands/` | 标准化入口 |
| `context/team/` | 团队级规范 |
| `.service-matrix/dependencies.yaml` | 服务拓扑与仓库路径 |
| `requirements/` | 需求生命周期产物 |
| `scripts/install.sh` | 多运行时渲染 |

`scripts/install.sh` 把 `.codebuddy/` 规范渲染到 `.claude/` / `.gemini/` / `.codex/` / `.continue/` 各 CLI 本地目录（gitignored 镜像），修改规范只改一处，不同 CLI 自动受益。

## 核心结论

> **Superpower 提升个人战斗力，Harness Engineering 建设团队作战体系。**

Context Engineering + Spec-First + Knowledge as Code，构成了可验证、可演进的 AI 协作工程基线。
