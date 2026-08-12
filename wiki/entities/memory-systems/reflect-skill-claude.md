---
title: reflect-skill-claude
type: entity
created: 2026-08-12
updated: 2026-08-12
sources:
  - https://github.com/hansvangent/reflect-skill-claude
  - raw/articles/hansvangent-reflect-skill-claude.md
tags:
  - memory
  - skill
  - session-extraction
  - claude-code
  - claude-md
---

# reflect-skill-claude (`/reflect`)

**`/reflect`** 是 [hansvangent](https://github.com/hansvangent) 出品的 Claude Code 3 阶段 retrospective skill（10 ⭐，2026-08）。**单文件 SKILL.md** 放进 `~/.claude/skills/reflect/`，手动 `/reflect` 触发（`disable-model-invocation: true`，不会后台跑）。

## 三阶段

| 阶段 | 触发条件 | 干什么 |
|------|---------|--------|
| **Phase 1: Extract** | 总是 | 把会话拆四桶：Durable facts / Standing corrections / Repeatable workflows→**新 skill** / Ephemeral（丢弃但报告）|
| **Phase 2: Optimize** | 总是 | 审计已有 `CLAUDE.md`，提出编号修改列表（带 trace 到具体时刻），逐项 `apply N` 批准 |
| **Phase 3: Compress** | 文件超 250 行 / project 300 行 / 显式 `/reflect compress` / Phase 2 出现 ≥3 contradiction | 重写压缩，行级不丢规则 |

## 命令语法

```bash
/reflect                  # Phase 1→2→3
/reflect project          # 扩到 project 的 CLAUDE.md + .claude.local.md
/reflect compress         # 只跑 Phase 3
/reflect compress project # 双文件压缩
```

## Phase 1 四桶

1. **Durable facts**（关于你：角色/工具/环境/偏好）→ `~/.claude/CLAUDE.md`
2. **Standing corrections**（"stop doing X" / "always do Y"）→ 带 WHY + WHEN 追加
3. **Repeatable workflows**（过"会再跑吗"门槛）→ **保存为新 skill**
4. **Ephemeral**（项目特有）→ 丢弃但列在报告里可拉回

> Phase 1 立即保存，**没有 review 步骤**——你从最终报告里回改。

## Phase 2 审计项

- CLAUDE.md 与 observed behavior 的矛盾
- 可合并的冗余
- stale references（用 grep/Read 验证）
- 重复批准的 permissions（→ allowlist）
- 需要 rename/补参/换响应形状的已有命令
- 手工重复 3+ 次的多步模式（候选新命令）
- 偏离实际功能的 skill 描述
- 工具调用低效（重读未变文件、本可并行的串行调用）
- 即使你没 comment 也会 flag 的 self-caught mistake

## Phase 3 压缩桶（6 类）

只当 `CLAUDE.md` 超阈值或显式触发。读文件 vs codebase truth，提议**行级**压缩。

## 安装

```bash
mkdir -p ~/.claude/skills/reflect
curl -fsSL https://raw.githubusercontent.com/hansvangent/reflect-skill-claude/main/SKILL.md \
  -o ~/.claude/skills/reflect/SKILL.md
# 重启 Claude Code
```

## 与同类对比

| 维度 | reflect-skill-claude | [[cc-analyst]] | [[ccflash]] |
|------|---------------------|----------------|-------------|
| **形式** | 单文件 skill | 完整 CLI | 单文件 skill |
| **自动触发** | ❌（手动）| ✅（定时可）| ❌（手动）|
| **新 skill 生成** | ✅ Phase 1 Bucket 3 | ✅ 提议 | ❌ |
| **CLAUDE.md patch** | ✅ Phase 2 提议 | ✅ 提议 | ❌ |
| **压缩** | ✅ Phase 3 | ❌ | ❌ |
| **回滚** | ❌ | ✅ | ❌ |
| **Stars** | 10 | 0 | 7 |

## 相关页面

- [[Session-Extraction-Pipeline]] — 范式
- [[cc-analyst]] — 同类
- [[Open-Amnesia|Open Amnesia]] — 同类但多源
