---
title: Hermes Skill Nudge
type: concept
created: 2026-05-06
updated: 2026-05-06
sources:
  - self:2026-05-06 session debugging
tags:
  - hermes
  - skill
  - gateway
  - feedback-loop
---

# Hermes Skill Nudge

> Hermes Gateway 的"技能建议"自动生成机制 — 每 N 次 iteration 主动建议用户保存当前会话中的操作模式为可复用 Skill

## 机制概述

Skill Nudge 是 Hermes 的内置反馈闭环：当用户在一次会话中反复做同类操作（tool call 达到一定次数）时，自动建议用户把操作模式固化为 Skill，下次同类任务无需重复描述。

核心逻辑在 `run_agent.py` 的 `run_conversation()` 末尾。

## 触发条件

```python
# run_agent.py:13919
if (self._skill_nudge_interval > 0
        and self._iters_since_skill >= self._skill_nudge_interval
        and "skill_manage" in self.valid_tool_names):
    _should_review_skills = True
    self._iters_since_skill = 0
```

三个条件必须同时满足：
1. `_skill_nudge_interval > 0`（默认 10，可在 `config.yaml` 的 `skills.creation_nudge_interval` 修改）
2. `self._iters_since_skill >= 10`（自上次 skill_manage 调用后的 iteration 数）
3. `skill_manage` 在当前平台的 `valid_tool_names` 中（`_HERMES_CORE_TOOLS` 包含它，所有平台都满足）

### ⚠️ 关键区分：Iteration ≠ Tool Call

**Iteration** = 一次 LLM API 调用的完整往返（发请求 → 收到响应 → 工具执行 → 结果返回）
**Tool Call** = 模型在单次迭代中决定调用的单个工具

```python
# run_agent.py:10822 — 每次 API 调用才算一个 iteration
while (... api_call_count < max_iterations ...):
    api_call_count += 1
    self._iters_since_skill += 1  # ← 每个 iteration +1，不是每个 tool call
```

举例：模型一次响应里批量调用了 `terminal` + `read_file` + `patch`，这算 **1 个 iteration**，不是 3 个。

所以即使工具调用次数很多，iteration 数可能远低于预期。

### 计数器重置

计数器在以下时机重置为 0：
- Skill nudge 被触发时（`run_agent.py:13923`）
- 用户实际调用了 `skill_manage` 时（`run_agent.py:9471, 9880`）

## 消息投递链路（异步）

触发后，nudge 消息通过异步 callback 发送，**不阻塞主响应**：

```
run_conversation() 末尾
  → _should_review_skills == True
  → agent.background_review_callback(message)
  → _bg_review_send(message)        # gateway/run.py:12880
    → _bg_review_release 未 set？
         → 加入 _bg_review_pending 队列
      已 set？
         → _deliver_bg_review_message()
           → asyncio.run_coroutine_threadsafe(
               _status_adapter.send(chat_id, message, metadata)
             )
```

### Release 机制：何时真正发送

`_bg_review_release` 是一个 `threading.Event`，在主 response 发送完毕后才 set：

```python
# gateway/run.py:12894
if getattr(type(_status_adapter), "register_post_delivery_callback", None) is not None:
    _status_adapter.register_post_delivery_callback(
        session_key, _release_bg_review_messages, generation=run_generation
    )
```

`register_post_delivery_callback` 是 `BasePlatformAdapter` 的方法，FeishuAdapter 和 TelegramAdapter 都继承了该实现（写在 `base.py` 里，不是 override）。所以两个平台都会走 `pop_post_delivery_callback` → 触发 `_release_bg_review_messages` → set `_bg_review_release` → 清空 pending 队列并投递。

## 已知问题

### 问题 1：nudge 仅在 review 有实质结果时才发送

nudge 消息通过 `background_review_callback` 发送，但该 callback **仅在 review 发现了可固化的内容时才被调用**：

```python
# run_agent.py:3662-3669
_bg_cb = self.background_review_callback
if _bg_cb:
    try:
        _bg_cb(f"💾 Self-improvement review: {summary}")
    except Exception:
        pass
```

如果 background review agent 说 "Nothing to save."（没有发现值得固化的内容），`actions` 为空，callback 永远不会被调用 → **nudge 消息永远不会出现**。

所以"有 10+ 次工具调用却没有自动生成 SKILL"的真实原因是：**review agent 没有发现值得固化的内容**，而不是代码 bug。

这也解释了为什么用户主动调用 `skill_manage` 能成功保存（因为是用户主动触发的），但 nudge 不会主动提示"你应该创建一个 skill"。

### 问题 2：投递异常静默

即使 review 发现了内容需要发送，`_deliver_bg_review_message` 中的异常被 `logger.debug` 吞掉：

```python
# gateway/run.py:12869
except Exception as _e:
    logger.debug("background_review_callback error: %s", _e)  # INFO 级别看不到
```

### Bug 1 澄清（llm-wiki 早期记录的误解）

早期文档声称 `FeishuAdapter` 的方法是 `send_message()` 而非 `send()`，这是错误的。`feishu.py:2148` 明确定义了 `async def send()`，方法签名是正确的。


## 为什么 session `session_20260507_120502_fdd890`（oneclickvirt）没有看到 nudge

该 session 68 次工具调用，`skill_manage` 在 iteration 57 被主动调用：

- Iteration 1-9：counter 1-9，< 10，不触发
- Iteration 10：counter=10，**nudge 触发**，spawn 背景 review → review 说 "Nothing to save." → **无消息发出**
- Iteration 11-56：counter 11-56，不重复触发（单次 `_should_review_skills=True`）
- Iteration 57：`skill_manage` 被主动调用 → counter 重置为 0
- Iteration 58-66：counter 1-9，< 10，不触发
- Iteration 67：counter=10，**nudge 再次触发**，spawn 背景 review → review 说 "Nothing to save." → **无消息发出**

**结论**：nudge 确实触发了两次，但两次 background review 都认为"没有值得固化的内容"（因为用户尚未表达偏好/纠正），所以没有任何消息发给用户。这不是 bug，是预期行为。

## 修复方向

~~nudge 只在有实质结果时才发~~ → ✅ **已修复**（2026-05-07）

现在 `actions` 为空时也会发送 `💾 Self-improvement review: nothing new to save`，用户能感知 review 已跑过。

剩余待修复：
- **投递异常**：`gateway/run.py:12869` 的 `logger.debug` → `logger.warning`，方便排查

## 相关代码路径

| 文件 | 行号 | 内容 |
|------|------|------|
| `run_agent.py` | 10867-10869 | 计数器累加 |
| `run_agent.py` | 13919-13923 | 触发条件检查 |
| `run_agent.py` | 13927-13938 | 调用 `background_review_callback` |
| `run_agent.py` | 3565-3674 | `_spawn_background_review` / 背景 review 逻辑 |
| `run_agent.py` | 3496-3556 | `_summarize_background_review_actions` — 只在有 created/updated/added 时返回 actions |
| `run_agent.py` | 3363-3437 | `_SKILL_REVIEW_PROMPT` — review agent 的提示词 |
| `gateway/run.py` | 12856-12888 | `_deliver_bg_review_message` / `_bg_review_send` |
| `gateway/run.py` | 12894-12903 | 注册 post_delivery_callback |
| `gateway/platforms/base.py` | 2088-2128 | `register_post_delivery_callback` / `pop_post_delivery_callback` |
| `gateway/platforms/feishu.py` | 2148 | `async def send()` — 方法签名正确 |
| `toolsets.py` | 41 | `_HERMES_CORE_TOOLS` 含 `skill_manage` |
