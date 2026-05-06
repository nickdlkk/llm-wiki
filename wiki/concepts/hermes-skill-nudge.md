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

## 已知 Bug：nudge 消息静默失败

### Bug 1：`send()` 方法签名错误

`_deliver_bg_review_message` 调用 `_status_adapter.send(chat_id, message, metadata=_status_thread_metadata)`，但 FeishuAdapter 的实际方法是 `send_message()`（`feishu.py:2148`）。

虽然 `BasePlatformAdapter` 定义了 `send()` 作为 abstract method，但子类实现为 `send_message()`，导致 `asyncio.run_coroutine_threadsafe` 抛出 `AttributeError`，被静默吞掉：

```python
# gateway/run.py:12869
except Exception as _e:
    logger.debug("background_review_callback error: %s", _e)  # DEBUG 级别，生产环境不打印
```

### Bug 2：DEBUG 日志默认关闭

`gateway.log` 里没有 `background_review_callback error` 记录，因为 Hermes Gateway 日志级别默认 INFO，`logger.debug(...)` 不会输出。问题无法从日志发现。

## 为什么当前会话没触发

当前 session（`20260506_173455`，Feishu DM）：
- Gateway 16:36 重启，这是之后的新 session
- AIAgent 缓存按 `session_key` 隔离，上一个 session 的 `_iters_since_skill` 不延续
- 排查过程大量是文本解释性回复，iteration 次数远低于 10
- 即便到达门槛，nudge 消息也会因 Bug 1 静默失败

之前的 Feishu session（`20260506_172415`，118 次工具调用）很可能是触发过的，但用户没看到提示，因为 `send()` → `AttributeError` → 被 debug 日志吞掉。

## 修复方向

1. `_deliver_bg_review_message` 应调用 `_status_adapter.send_message()` 而非 `.send()`
2. 将 `background_review_callback error` 从 `logger.debug` 改为 `logger.warning`
3. 或者在 `BasePlatformAdapter` 层统一接口，让 `send()` 和 `send_message()` 共存

## 相关代码路径

| 文件 | 行号 | 内容 |
|------|------|------|
| `run_agent.py` | 10867-10869 | 计数器累加 |
| `run_agent.py` | 13919-13923 | 触发条件检查 |
| `run_agent.py` | 13927-13938 | 调用 `background_review_callback` |
| `gateway/run.py` | 12856-12888 | `_deliver_bg_review_message` / `_bg_review_send` |
| `gateway/run.py` | 12894-12903 | 注册 post_delivery_callback |
| `gateway/platforms/base.py` | 2088-2128 | `register_post_delivery_callback` / `pop_post_delivery_callback` |
| `gateway/platforms/feishu.py` | 2148 | `async def send()` / 实际方法名是 `send_message()` |
| `toolsets.py` | 41 | `_HERMES_CORE_TOOLS` 含 `skill_manage` |
