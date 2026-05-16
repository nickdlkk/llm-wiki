---
title: AgentVerse
description: Multi-Agent Collaboration Platform — 多AI专家团队协作平台，支持自主对话、MCP工具集成、文件管理
github: https://github.com/Peiiii/AgentVerse
stars: 306
tags: [multi-agent, collaboration, team-chat, mcp, open-source]
created: 2026-05-16
---

# AgentVerse

## 概述

**AgentVerse** (多Agent协作平台) 是一个支持多AI智能体协作对话的开源平台。多个具有不同专业领域和个性的AI专家可以自主交流、协作讨论，为用户提供多角度的专业见解和解决方案。

**GitHub**: [Peiiii/AgentVerse](https://github.com/Peiiii/AgentVerse)  
**官网**: [agent.dimstack.com](https://agent.dimstack.com)  
**Language**: TypeScript  
**License**: MIT  
**Stars**: 306  
**Forks**: 77  

## 核心功能

### 1. 预设专家团队 (14个)

**思维探索类:**
- 思维探索团队 — 深度思考和创新
- 自由思考组 — 开放性思考和深度探讨
- 认知融合团队 — 概念转化和模式识别
- 情绪决策团队 — 情绪智能和决策优化
- 结构化思考团队 — 使用结构化框架解决问题
- 超级思维团队 — 全方位思维专家团队

**创作类:**
- 小说创作组 — 故事创作和剧情发展
- 创意激发组 — 创意发散和跨界思维
- 叙事探索团队 — 故事结构和多元可能性

**商业类:**
- 创业创新组 — 商业模式和市场分析
- 产品开发组 — 产品设计、开发和项目管理
- 实践执行团队 — 实际执行和项目落地

**其他:**
- 时间探索团队 — 时间视角和历史灵感
- Agent设计组 — AI Agent系统设计

### 2. 自定义Agent创建

- 对话式创建 — 通过自然语言对话创建专属AI智能体
- 智能配置助手 — AI助手帮你完善Agent配置
- 灵活定制 — 支持自定义角色、性格、专长、行为方式

### 3. MCP工具集成

支持 **Model Context Protocol (MCP)**，AI可使用外部工具:
- 文件系统操作
- 数据查询和分析
- 信息检索
- 自动化任务
- 代码生成和执行

### 4. 文件管理系统

内置基于LightningFS的文件系统:
- 文件/目录的增删改查
- 文件内容编辑和预览
- 文件搜索
- 文件上传和下载

### 5. 多讨论管理

- 支持多个独立的讨论话题
- 讨论状态控制（活跃/暂停）
- 消息历史记录
- 自动标题生成

## 技术架构

```
AgentVerse
├── 前端: TypeScript/React
├── 多Agent协作引擎
├── MCP协议适配器
├── 文件系统 (LightningFS)
└── 对话管理模块
```

## 应用场景

| 场景 | 描述 |
|------|------|
| 头脑风暴 | 不同思维方式的AI专家一起激发创意 |
| 产品设计 | 产品经理、开发者、设计师一起讨论方案 |
| 决策辅助 | 获取多个专业角度的分析和建议 |
| 学习辅导 | 多专家讲解不同角度的知识 |

## 与其他工具对比

| 工具 | 定位 | 协作模式 |
|------|------|----------|
| AgentVerse | 多AI专家协作 | 群聊讨论 + 角色设定 |
| ClawPort | OpenClaw仪表盘 | UI dashboard + chat |
| Vibe Kanban | AI代码执行+Review | 看板 + Git worktree |
| Aperant | 自主多会话编码 | 端到端自动化 |

## 相关链接

- [GitHub Repository](https://github.com/Peiiii/AgentVerse)
- [在线演示](https://agent.dimstack.com)
