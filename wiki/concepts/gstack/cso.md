---
title: gstack /cso
type: concept
created: 2026-05-16
updated: 2026-05-16
sources: ["raw/refs/gstack/cso/SKILL.md"]
tags: [gstack, security, cso, threat-modeling, owasp, audit]
---

# /cso

Chief Security Officer mode. Infrastructure-first security audit: secrets archaeology, dependency supply chain, CI/CD pipeline security, LLM/AI security, skill supply chain scanning, plus OWASP Top 10, STRIDE threat modeling, and active verification.

## 核心職責

**何時觸發**：
- 用戶說 "security audit"、"threat model"、"pentest review"、"OWASP"、"CSO review"

**兩種模式**：
- **Daily Mode**：Zero-noise，8/10 confidence gate（日常輕量審計）
- **Comprehensive Mode**：Monthly deep scan，2/10 bar（深度安全掃描）

**審計維度**：
- Secrets Archaeology（敏感信息考古）
- Dependency Supply Chain（依賴供應鏈）
- CI/CD Pipeline Security（CI/CD 安全）
- LLM/AI Security（AI 安全）
- Skill Supply Chain Scanning（技能供應鏈掃描）
- OWASP Top 10
- STRIDE Threat Modeling（威脅建模）
- Active Verification（主動驗證）

## Voice Triggers

- "see-so"
- "see so"
- "security review"
- "security check"
- "vulnerability scan"
- "run security"

## 與 /review 的區別

| 維度 | /cso | /review |
|------|------|---------|
| 範圍 | 基礎設施級安全 | 代碼結構級審查 |
| 重點 | 威脅建模、滲透測試 | 結構性 bug、N+1 |
| 觸發時機 | 安全審計 | Pre-landing |

## 相關技能

- [[entities/concepts-frameworks/gstack]] — gstack 完整 entity 入口
- [[concepts/gstack/review]] — 代碼級安全審查
