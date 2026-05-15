---
title: "writing-skills"
type: concept
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/mok888/superpower-with-files
  - raw/superpower-with-files/skills/writing-skills/SKILL.md
tags:
  - skills
  - skill-authoring
  - TDD
  - meta-skill
  - superpower-with-files
---

# writing-skills

> **TDD 应用于过程文档**。创建新 skill、编辑现有 skill、或部署前验证 skill 工作时使用。

**核心原则**：如果没看到 agent 在无 skill 情况下 fail，你不知道 skill 教的是否正确。

## TDD 在 Skills 中的映射

| TDD 概念 | Skill 创建 |
|---------|-----------|
| **Test case** | Pressure scenario with subagent |
| **Production code** | Skill document（SKILL.md）|
| **Test fails（RED）** | Agent 无 skill 时违反规则（baseline）|
| **Test passes（GREEN）** | Agent 有 skill 时遵守 |
| **Refactor** | 关闭漏洞同时保持合规 |
| **Write test first** | 写 skill 前运行 baseline 场景 |
| **Watch it fail** | 记录 agent 使用的精确合理化 |
| **Minimal code** | 写针对那些特定违反的 skill |
| **Watch it pass** | 验证 agent 现在遵守 |
| **Refactor cycle** | 发现新合理化 → 堵住 → 再验证 |

**整个 skill 创建过程遵循 RED-GREEN-REFACTOR。**

## 何时创建 Skill

**创建当：**
- 技术对你来说不是直观明显的
- 会跨项目参考
- 模式应用广泛（非项目特定）
- 其他人会受益

**不为以下创建：**
- 一次性解决方案
- 其他地方有完善文档的标准实践
- 项目特定约定（放 CLAUDE.md）
- 如果可用 regex/validation 强制执行（自动化；文档留给判断）

## Skill 类型

| 类型 | 说明 | 例子 |
|------|------|------|
| **Technique（技术）** | 有步骤要遵循的具体方法 | condition-based-waiting, root-cause-tracing |
| **Pattern（模式）** | 思考问题的方式 | flatten-with-flags, test-invariants |
| **Reference（参考）** | API 文档、语法指南、工具文档 | office docs |

## SKILL.md 结构

**Frontmatter（YAML）：**
- 只支持两个字段：`name` 和 `description`
- 最大 1024 字符
- `description`：第三人称，只描述何时使用（**不是**做什么）

```yaml
---
name: Skill-Name-With-Hyphens
description: Use when [specific triggering conditions and symptoms]
---
```

### Claude Search Optimization（CSO）

**描述 = 何时使用，不是 skill 做什么**

```yaml
# ❌ 坏：总结 workflow — Claude 可能按描述走而非读完整 skill
description: Use when executing plans - dispatches subagent per task with code review between tasks

# ✅ 好：只有触发条件，无 workflow 总结
description: Use when executing implementation plans with independent tasks in the current session
```

**为什么重要：** 测试揭示当描述总结 workflow 时，Claude 可能跟随描述而不读完整内容。

## 铁律（与 TDD 相同）

```
NO SKILL WITHOUT A FAILING TEST FIRST
```

适用于**新 skill 和编辑现有 skill**。无例外。

## 测试所有 Skill 类型

### Discipline-Enforcing Skills（规则/要求）

**例子**：TDD, verification-before-completion, designing-before-coding

**测试用：**
- 学术问题：他们理解规则吗？
- 压力场景：压力下他们遵守吗？
- 多压力组合：time + sunk cost + exhaustion

### Technique Skills（如何做指南）

**例子**：condition-based-waiting, root-cause-tracing

**测试用：**
- 应用场景：他们能正确应用技术吗？
- 变化场景：他们处理 edge case 吗？

### Pattern Skills（心智模型）

**例子**：reducing-complexity, information-hiding concepts

**测试用：**
- 识别场景：他们认到模式适用吗？
- 应用场景：他们能使用心智模型吗？

## 反模式

**❌ 叙事示例**："In session 2025-10-03, we found empty projectDir caused..."
**为什么坏：** 太特定，不可复用

**❌ 多语言稀释**：example-js.js, example-py.py, example-go.go
**为什么坏：** 质量平庸，维护负担

**❌ 流程图中的代码**：
```dot
step1 [label="import fs"];
step2 [label="read file"];
```
**为什么坏：** 不能复制粘贴，难读

**❌ 通用标签**：helper1, helper2, step3, pattern4
**为什么坏：** 标签应有语义

## 在 superpower-with-files 中的角色

writing-skills 是**元技能（meta-skill）**——它是 superpower-with-files 框架自我进化的机制。通过 TDD 方式创建和验证 skill，框架不断积累可复用的工程知识。它是 15 个 skill 中唯一一个用于构建更多 skill 的 skill。
