---
title: Mattpocock Skills 对比分析
type: concept
created: 2026-04-29
updated: 2026-04-29
sources:
  - /root/llm-wiki/raw/mattpocock-skills/
tags:
  - skill-design
  - comparison
  - mattpocock
  - socratic
  - workflow
---

# Mattpocock Skills 对比分析

## 概览

`mattpocock/skills` 包含 **16 个 skill**，分布在三个目录：

| 目录 | Skills |
|------|--------|
| `engineering/` | diagnose, **grill-with-docs**, improve-codebase-architecture, setup-matt-pocock-skills, tdd, to-issues, **to-prd**, triage, zoom-out |
| `productivity/` | caveman, **grill-me**, write-a-skill |
| `misc/` | git-guardrails-claude-code, migrate-to-shoehorn, scaffold-exercises, setup-pre-commit |

## 横向对比：grill-me vs 其他 Skill

### 一、grill-me — 极简 Socratic 追问

```
字数：约 80 词（3 句话）
结构：无 YAML frontmatter，无 frontmatter name
触发条件：用户说 "grill me" / "stress-test" / "grill me on design"
终止条件：未明确说明，依赖 AI 自己判断
```

**设计哲学**：「越薄越好」——不给任何流程图、状态机或参考文档。

---

### 二、grill-with-docs — grill-me 的生产增强版

`grill-with-docs` 是 `grill-me` 的**重度版本**，增加了：

| 增加的内容 | 说明 |
|-----------|------|
| **CONTEXT.md 实时更新** | 追问同时把术语冲突实时写入项目词汇表 |
| **ADR 按需生成** | 仅当三个条件同时满足才建议写 ADR（难以逆转 + 上下文缺失会让人惊讶 + 真实权衡） |
| **文件结构感知** | 知道 `CONTEXT-MAP.md` 指向多 context 场景 |
| **术语对账** | 发现用户说的词与 CONTEXT.md 词义冲突时立即指出 |
| **代码交叉验证** | 用户说「系统如何工作」，skill 去读代码并报告矛盾 |

**关键洞察**：grill-me 是**离线**的（不写任何文件），grill-with-docs 是**在线**的（直接修改项目文件）。两者用同一套追问引擎，但接入深度不同。

---

### 三、Skill 复杂度光谱

```
极简 ───────────────────────────────────────────────────────────── 厚重
grill-me → zoom-out → caveman → to-prd → write-a-skill → tdd → triage
  80词      1句       200词     PRD模板   元技能+模板  哲学+流程  状态机+角色
```

| Skill | 字数 | 核心模式 |
|-------|------|---------|
| `zoom-out` | 1 句 | 纯指令 |
| `grill-me` | ~80 词 | 纯指令 |
| `caveman` | ~200 词 | 持久规则集 |
| `to-prd` | ~500 词 | PRD 模板 |
| `write-a-skill` | ~800 词 | 元技能 + 模板 |
| `tdd` | ~1000 词 | 哲学 + 流程 + 检查清单 |
| `triage` | ~2000 词 | 状态机 + 角色 + 流程 |

---

### 四、设计模式对比

#### 1. 触发机制

| Skill | 触发方式 |
|-------|---------|
| `grill-me` | 用户主动说 "grill me" |
| `grill-with-docs` | 用户说 "stress-test against docs" |
| `tdd` | 用户说 "red-green-refactor" / "test-first" |
| `caveman` | 用户说 "caveman mode" / "less tokens" |
| `triage` | 用户调用 `/triage` 命令 |

#### 2. 执行模式

| Skill | 执行模式 |
|-------|---------|
| `grill-me` | 对话循环（AI 问 → 用户答 → AI 继续问） |
| `tdd` | 垂直切片循环（RED→GREEN→REFACTOR） |
| `triage` | 状态机流转（unlabeled → needs-triage → ready-for-agent 等） |
| `to-prd` | 单程合成（对话 → PRD，不回头） |
| `caveman` | 持久覆盖（一旦激活，所有回复持续压缩） |
| `zoom-out` | 单次执行（读代码 → 输出地图） |

#### 3. 终止条件明确性

| Skill | 终止条件 |
|-------|---------|
| `tdd` | 所有计划中的 behavior 测试通过 |
| `triage` | issue 获得 category + state role |
| `to-prd` | PRD 发布到 issue tracker |
| `grill-me` | ❌ 未定义，依赖 AI 主观判断「达成共识」 |
| `caveman` | 用户说 "stop caveman" |
| `zoom-out` | 输出模块地图 |

---

### 五、grill-me 的独特性

**grill-me 是唯一一个没有 frontmatter `name` 字段的 skill。**

```yaml
# grill-me — 无 name
---
name: grill-me    ← 不存在
description: ...
---

# write-a-skill — 有 name
---
name: write-a-skill
description: ...
---
```

这说明 `name` 是可选的，描述足够清晰时 agent 可以自行判断。

---

### 六、对 Skill 设计者的启示

#### 极简 vs 厚重的权衡

| 维度 | 极简（grill-me） | 厚重（tdd/triage） |
|------|-----------------|-------------------|
| **token 消耗** | 低 | 高 |
| **可靠性** | 依赖模型推理质量 | 流程强制，偏差小 |
| **适应性** | 高（无结构约束） | 低（过度约束） |
| **适合场景** | 开放性讨论 | 规范性执行 |
| **风险** | 「追问」可能变成「复读」 | 变成纸上谈兵，执行率下降 |

#### grill-me 的局限

1. **无终止条件定义** — 「until we reach shared understanding」是主观判断，不同模型可能提前终止
2. **无记录机制** — 不写任何产物（grill-with-docs 会写 CONTEXT.md）
3. **不区分场景** — 任何 plan 都用同一套追问框架，没有针对「bug fix」vs「new feature」的不同问法

---

### 七、grill-me 在工具链中的位置

```
用户提出计划/设计
      ↓
[ grill-me ] ──── 追问到底，直至共识
      ↓
[ triage ] ─────── 分类 + 状态角色
      ↓
[ to-prd ] ─────── 合成 PRD
      ↓
[ tdd ] ────────── 测试驱动实现
```

`grill-me` 是工作流的最上游——在动手之前先把设计搞清楚。这和 SDD（Spec-Driven Development）精神一致，但用「对话」而非「文档」作为共识载体。
