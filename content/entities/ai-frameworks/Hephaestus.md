---
title: Hephaestus
tags:
  - AI-Agent
  - Python
  - Agentic-Framework
  - Semi-Structured
  - Multi-Agent
  - Dynamic-Workflow
  - AGPL-3.0
---

# Hephaestus

> **Repo**: [Ido-Levi/Hephaestus](https://github.com/Ido-Levi/Hephaestus)
> **License**: AGPL-3.0
> **Language**: Python 3.8+
> **Stars**: 1.1k
> **定位**: Semi-Structured Agentic Framework — 动态工作流构建框架

## 核心定位

Hephaestus 是一个**半结构化 Agent 编排框架**，核心创新是：让工作流在运行时**自我构建**，而非预先定义所有分支。

传统 Agent 框架的局限：每个分支都需要预设指令，你必须提前写出所有可能场景的任务描述。

Hephaestus 的思路：**定义逻辑阶段类型（如 Plan → Implement → Test），让 Agent 根据实际发现自主创建任意阶段的任务。**

## 核心概念：Phase-Based Dynamic Branching

### 三种 Phase 类型

| Phase | 职责 | 示例 |
|-------|------|------|
| **Phase 1 (Analysis)** | 理解、规划、调查 | 分析 PRD、调查性能问题 |
| **Phase 2 (Implementation)** | 构建、修复、优化 | 实现功能、修复 Bug |
| **Phase 3 (Validation)** | 测试、验证、质量检查 | 运行测试、验证修复 |

### 关键创新：Agent 可在任意 Phase 创建任务

传统工作流需要预设分支逻辑：
```
IF optimization_found THEN spawn_investigation_task
```

Hephaestus 的做法：
```
Agent 在 Phase 3 发现优化机会 → 直接创建 Phase 1 调查任务 → 工作流自动分支
```

无需预设"发现优化时做什么"，Agent 自主决定。

## 动态工作流示例

```
PRD 输入 → Phase 1 分析 → 生成 5 个 Phase 2 任务（并行）
                    ↓
         Phase 2 Agent 完成 API → 触发 Phase 3 测试
                    ↓
         Phase 3 测试发现缓存优化机会 → 创建 Phase 1 调查任务（新分支）
                    ↓
         Phase 1 确认可行 → Phase 2 实现 → Phase 3 验证
```

## Hephaestus Dev：开箱即用的开发工作流

Hephaestus 提供 5 个生产级开发工作流：

| Workflow | 功能 |
|----------|------|
| **PRD to Software Builder** | 从 PRD 构建完整软件 |
| **Bug Fix** | 系统性分析、修复、验证 Bug |
| **Index Repository** | 扫描代码库建立知识记忆 |
| **Feature Development** | 为现有代码库添加功能 |
| **Documentation Generation** | 为现有代码库生成文档 |

```bash
python run_hephaestus_dev.py --path /path/to/project
```

## 技术架构

| 组件 | 说明 |
|------|------|
| **Guardian** | 监控 Agent 轨迹，维持工作流连贯性（coherence） |
| **Phase Scheduler** | 动态调度 Phase 1/2/3 任务 |
| **Agent Pool** | 多 Agent 并行执行 |
| **Task Graph** | 运行时构建的动态任务图 |

## 与其他 Agent 框架对比

| | Hephaestus | OpenHands | Multi-agent Orchestrators |
|---|---|---|---|
| **工作流构建** | 运行时自我构建 | 预设工作流 | 预设模板 |
| **分支触发** | Agent 自主发现 | 预设分支条件 | 预设分支条件 |
| **Phase 模型** | 3 Phase 循环 | 无 | 无 |
| **License** | AGPL-3.0 | MIT | 各异 |

## 安装

```bash
pip install hephaestus

# Hephaestus Dev
python run_hephaestus_dev.py --path /path/to/project
```

## 适用场景

✅ **适用**：复杂软件项目、PRD 驱动的开发、持续发现新问题的任务
❌ **不适用**：简单单一任务、已有清晰步骤的增量开发

## 参考

- [Hephaestus Documentation](https://ido-levi.github.io/Hephaestus/)
- [Discord Community](https://discord.gg/FMCJeKG3dU)
