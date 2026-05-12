---
title: Memorizz Summary
type: summary
created: 2026-05-12
updated: 2026-05-12
sources:
- https://github.com/RichmondAlake/memorizz
tags:
- memory-systems
- distillation
---

# memorizz — 蒸馏笔记

## 基本信息

- **Repo**: RichmondAlake/memorizz
- **Stars**: 723
- **语言**: Python
- **License**: PolyForm Noncommercial 1.0.0（**非商业用途**）
- **定位**: 实验性/教育性框架，API 可能变更，未生产加固

## 核心价值

MemoRizz 的设计亮点是**存储后端抽象**（MemoryProvider 接口）和**多种记忆类型正交组合**。通过 `MemAgentBuilder` 可按需组合：

```python
agent = (
    MemAgentBuilder()
    .with_application_mode(ApplicationMode.DEEP_RESEARCH)
    .with_memory_provider(provider)  # Oracle / MongoDB / Filesystem
    .with_llm_config(llm_config)
    .with_semantic_cache(enabled=True, threshold=0.85)
    .build()
)
```

10种记忆类型覆盖了 episodic（对话）、semantic（知识）、procedural（工具）、short-term（工作区）、shared（多 Agent 黑板）完整认知层。

## 值得关注的设计

1. **Semantic Cache** — 相似查询直接返回缓存，threshold 0.85 以上的才命中，这是减少 token 成本的实用设计
2. **Automation Worker** — 定时任务通过 Oracle 持久化 job，Worker loop 轮询执行，支持 WhatsApp 投递 —— 是记忆系统+主动推送的少见组合
3. **Context Window Stats** — `get_context_window_stats()` 让 Agent 主动感知上下文稀缺性，可驱动摘要压缩决策

## 蒸馏结论

**MemoRizz 是最佳的学习参考**（10种记忆类型 × 3种存储后端的正交设计），但生产慎用（License + 实验性双重风险）。架构设计可借鉴，代码不可直接用于商业产品。
