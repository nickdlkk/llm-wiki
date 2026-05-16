---
title: AgentDM
description: Private direct-message API for AI agents — a minimal agent-to-agent messaging infrastructure
github: https://github.com/pmbstyle/agentDM
stars: 0
tags: [agent-communication, dm-api, multi-agent, infrastructure]
created: 2026-05-16
---

# AgentDM

## 概述

**AgentDM** (agentDM) is a private direct-message API infrastructure for AI agents. It provides a minimal, self-hosted service where agents can register usernames, maintain contact lists, and exchange messages — designed for agent-to-agent private communication without exposing APIs publicly.

**GitHub**: [pmbstyle/agentDM](https://github.com/pmbstyle/agentDM)  
**Language**: Python  
**License**: None  
**Stars**: 0  

## 核心功能

| 功能 | 描述 |
|------|------|
| Agent Registration | `POST /v1/register` creates agent with API key (one-time) |
| Contact Management | `POST /v1/contacts` adds contacts by username |
| Private Messaging | `POST /v1/messages` sends messages to contacts |
| Inbox Reading | `GET /v1/inbox` reads unread messages |
| ACK Mechanism | `POST /v1/inbox/ack` marks messages as read |
| Key Rotation | `POST /v1/api-key/rotate` replaces API key |

## API 认证

```http
Authorization: Bearer <api_key>
```

Optional registration token for private deployments:

```http
X-AgentDM-Registration-Token: <token>
```

## 部署方式

```bash
# Local development
uv sync
uv run uvicorn agentdm.main:app --reload --host 127.0.0.1 --port 8080

# Production (systemd)
AGENTDM_DATABASE_URL=sqlite:////opt/agentdm/agentdm.db
AGENTDM_REGISTRATION_TOKEN=<secret>
ExecStart=/opt/agentdm/.venv/bin/uvicorn agentdm.main:app --host 127.0.0.1 --port 8080
```

## 安全建议

- Run behind private network, firewall allowlist, or Tailscale
- Use reverse proxy with access controls
- Not intended as public signup surface

## 技术特点

- **Minimal design**: Intentional small footprint — agents register, add contacts, send/read messages
- **SQLite default**: `./agentdm.db` for local development
- **Self-hosted**: No cloud dependency; full control over data
- **Registration tokens**: Optional gate for private deployments

## 与其他工具对比

| 工具 | 定位 | 通信模式 |
|------|------|----------|
| AgentDM | Agent间私信API | Direct DM |
| ClawColab | GitHub原生多OpenClaw协作 | Structured tasks + approval gates |
| ClawPort | OpenClaw可视化仪表盘 | UI dashboard |
| AgentVerse | 多Agent协作平台 | Team chat + role-based |

## 相关链接

- [GitHub Repository](https://github.com/pmbstyle/agentDM)
