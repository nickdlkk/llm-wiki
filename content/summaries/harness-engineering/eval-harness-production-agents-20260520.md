---
title: "Building an Evaluation Harness for Production AI Agents: A 12-Metric Framework From 100+ Deployments"
summary: "生产级 AI Agent 的 12 指标评估框架，涵盖检索、生成、Agent 行为、生产健康四类，来自 100+ 企业部署实践"
source: "Towards Data Science"
url: "https://towardsdatascience.com/building-an-evaluation-harness-for-production-ai-agents-a-12-metric-framework-from-100-deployments/"
date: 20260520
tags: [evaluation, harness, production, RAG, multi-agent, observability]
---

# Building an Evaluation Harness for Production AI Agents: A 12-Metric Framework From 100+ Deployments

## 核心框架

12 指标分为四类：

### 检索类（4）

| 指标 | 目标 |
|------|------|
| Context Relevance | >0.85 |
| Context Recall | >0.90 |
| Context Precision (MRR) | >0.80 |
| Retrieval Latency p95 | <200ms |

### 生成类（3）

| 指标 | 目标 |
|------|------|
| Answer Faithfulness | >0.95 |
| Answer Relevance | >0.90 |
| Hallucination Rate | <2% |

### Agent 类（3）

| 指标 | 目标 |
|------|------|
| Tool Selection Accuracy | >0.92 |
| Tool Execution Success | >0.98 |
| Multi-Step Coherence | >0.85 |

### 生产类（2）

| 指标 | 目标 |
|------|------|
| Cost per Query | <$0.05 |
| P99 Latency | <3s（对话）、<10s（分析） |

## 关键洞察

**三个致命反模式：**

1. **"MVP后再加评估"** ——  retrofit 需要 4-6 周，且数据 lag 无法及时catch回归
2. **"准确率够用"** ——  benchmark 95% 准确率不等于生产无幻觉，真实用户query分布完全不同
3. **"人工抽检够用"** ——  每日 100 次可以，10000 次必然崩溃

**检索是根基：**  大多数 RAG 失败根因在检索而非生成。模型只能处理喂给它的内容，10个块里只有3个相关，上游污染无法靠 prompt 修复。

**Agent 专用陷阱：**
- 工具数量 >12 时，选择准确率从 95% 崩到 70%
- 2步 trace 95% coherence，6步 trace 跌到 60%
- 工具参数格式与 API schema 不匹配是最常见执行失败原因

**评估成本：** LLM-as-Judge 评估成本约为推理成本的 30-50%。$4k/月推理账单 → $1.2-2k/月评估。但第一次阻止的生产事故就值回全部投入。

## 实施路线

| 阶段 | 指标 | 时间 |
|------|------|------|
| Pre-launch (Week 0-2) | 检索三指标 + Faithfulness | 最多人死于这个阶段 |
| Soft launch (Week 3-6) | Hallucination + Relevance + Tool Selection | 真实流量暴露问题 |
| Production stable (Week 7+) | Cost + Latency + Execution Success + Coherence | 优化而非堵漏 |

## 工具链

- **Ragas** — RAG 指标最强开源
- **TruLens** — LangChain/LlamaIndex 集成更强
- **DeepEval** — Agent 指标覆盖更好
- **LangSmith** — 生产追踪，弱于离线 benchmark

## FAQ

- **最小起步套件：** Context Relevance + Answer Faithfulness + Tool Selection Accuracy，三项覆盖 70% 预发布失败
- **离线 vs 在线：** 离线跑 benchmark 集保准度，在线跑生产流量抓分布外问题，两者缺一不可
- **LLM-as-Judge 分离原则：** 裁判模型必须与生成模型不同家族，否则评分虚高

## 原文信息

- 作者：Pratik R（Intuz 联合创始人）
- 发表：2026-05-13，Towards Data Science
- 原文链接：https://towardsdatascience.com/building-an-evaluation-harness-for-production-ai-agents-a-12-metric-framework-from-100-deployments/
