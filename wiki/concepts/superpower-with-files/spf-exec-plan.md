---
title: "spf-exec-plan"
type: concept
created: 2026-05-16
updated: 2026-05-16
sources:
  - https://github.com/mok888/superpower-with-files
  - raw/superpower-with-files/skills/spf-exec-plan/SKILL.md
tags:
  - skills
  - execution
  - TDD
  - batch-execution
  - superpower-with-files
---

# spf-exec-plan

> 有书面实现计划时使用。在单独 session 中批次执行，批次间有审查检查点。

## 流程

### Step 1: 加载和审查计划

1. 读计划文件
2. 严格审查 — 识别任何问题或担忧
3. 有担忧？在开始前向用户提出
4. 无担忧？创建 TodoWrite 并继续

### Step 2: 执行批次

**默认：前 3 个任务**

每任务：
1. 标记为 in_progress
2. 严格按步骤执行（计划有咬碎步骤）
3. 按指定运行验证
4. 标记为 completed

### Step 3: 报告

批次完成后：
- 展示实现了什么
- 展示验证输出
- 说："Ready for feedback."

### Step 4: 继续

基于反馈：
- 按需应用变更
- 执行下一批次
- 重复直到完成

### Step 5: 完成开发

所有任务完成并验证后：
- 宣布使用 finishing-a-development-branch
- 完成工作

## 停止并求助

**立即停止执行当：**
- 批次中途遇阻（缺失依赖、测试失败、指令不清）
- 计划有阻止开始的重大缺口
- 不理解指令
- 验证反复失败

**宁可问澄清，不要猜测。**

## 返回审查

**回 Step 1 当：**
- Partner 基于反馈更新了计划
- 基本方法需要重新思考

**不要强突阻塞** — 停止并问。

## 集成

**必需工作流 skill：**
- **superpowers:using-git-worktrees** — 必需：在开始前设置隔离工作区
- **superpowers:writing-plans** — 创建本 skill 执行的计划
- **superpowers:finishing-a-development-branch** — 所有任务后完成开发

## 并行执行协议

如果任务 A 和任务 B 标记为 **`Parallel with`** 彼此：
- 必须尝试调度 **subagent** 处理其中一个任务
- 如果无 subagent 工具可用，必须顺序执行但在 `progress.md` 中承认并行潜力
- 调度前确保共同依赖（两者都依赖的任务）100% 完成

## 执行检查点规则

批次完成 3 个任务并暂停说 "Ready for feedback" 前，**必须**：

1. **Context Hygiene**：
   - 将批次总结为一个简洁段落写入 `progress.md`
   - 清理不再相关的临时发现
   - 为下一提示保留相关 `task_plan.md` 阶段和 `active_tdd_plan.md` 后续 3 任务

2. **Session Handoff**：
   - 如果停止或暂停超过几分钟，创建或更新 **`.superpower-with-files/handoff.md`**
   - 包含：最新时间戳、已完成任务、当前任务状态、任何阻塞、下一步详情

3. **隐含停止**：
   > "Batch complete. Progress saved and context compacted. Session handoff updated at `handoff.md`. Ready to continue?"

## 在 superpower-with-files 中的角色

spf-exec-plan 是 **Execution Phase 的批次驱动模式**。与 subagent-driven-development（连续子 agent 驱动）的区别：
- spf-exec-plan：批次（3 任务），人类在批次间审查，有 checkpoint
- subagent-driven-development：连续，每任务两阶段 agent 内部审查

两者是等价的执行选项，用户或任务特征决定用哪个。
