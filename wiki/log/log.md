# Wiki Log

> Chronological record of all wiki actions. Append-only.
> Format: `## [YYYY-MM-DD] action | subject`
> Actions: ingest, update, query, lint, create, archive, delete

## [2026-05-31] ingest | 高德 OPC 自主增长系统
- Source: 微信公众号 https://mp.weixin.qq.com/s/sZm-KDM7NoITchuhpbJkJQ
- Author: 曼行 / 阿里高德
- Summary: 多 Agent 分工 + 状态机 + Benchmark 评审体系的 OPC 自主增长实践
- Files created:
  - [[summaries/harness-engineering/gaode-autonomous-growth-20260531]]
  - raw/articles/gaode-autonomous-growth-20260531.md
- Files updated:
  - index.md (added entry under Harness Engineering section)

## [2026-08-06] ingest | Agent 开发指南：技术太多，该怎么学？
- Source: 微信公众号 https://mp.weixin.qq.com/s/Mx1pclSLzkRFXKEME24TYA
- Author: lencx
- Summary: 1999 行 / 174 处引用长文蒸馏，11 节框架：Agent 不缺代码缺可信完成；Vercel×Cloudflare 平台争夺；OpenClaw vs Hermes 路线；Electron vs Tauri 拓扑；Pet 注意力机制；语言分工；Skills 治理
- Files created:
  - raw/articles/lencx-agent-dev-guide-20260806.md (sha256: 4c8734a0... body-only)
  - [[summaries/harness-engineering/lencx-agent-dev-guide-20260806]]
  - [[concepts/agent-native-stack]] (新概念页：11 节 Agent 框架)
  - [[concepts/pet-attention-mechanism]] (新概念页：注意力协议 + 状态机投影)
- Files updated:
  - [[concepts/harness-engineering]] (补充 OpenAI 正式定义 + Anthropic Managed Agents 拆分 + 删减型 Harness)
  - index.md (新增 2 个 Concepts + 1 个 Summaries 条目)

## [2026-08-06] ingest | Agent评测漫谈 —— 由浅入深讲解Agent评测
- Source: 微信公众号 https://mp.weixin.qq.com/s/gZKWRqznB8sNBFf69fBIvw
- Author: 图灵Agent评测团队 / 美团
- Summary: 评测从答案打分走向任务系统行为评测；搭桥三层指标 + 人人/人机二元化对齐 + 数据飞轮；长程 Agent/Skill 评测七项基建
- Files created:
  - raw/articles/meituan-agent-evaluation-20260806.md (sha256: e850304c5... body-only)
  - [[summaries/harness-engineering/meituan-agent-evaluation-20260806]]

## [2026-08-06] ingest | 复杂业务团队的 AI Coding 交付实践：知识库、RD 流程和质量门禁
- Source: 微信公众号 https://mp.weixin.qq.com/s/aopO-3KO9lenKF5WHhBD7w
- Author: 寂秋 / 淘天集团-物流技术
- Summary: 知识库 5 层分层（main/applications/candidate/personal/template）+ RD 流程文件化（/rd:* 命令 + Markdown 落盘）+ 9 个质量门禁 fail-fast；代码采纳率 75%→95% 两轮案例
- Files created:
  - raw/articles/taotian-rd-workflow-20260806.md (sha256: b98f44a8d... body-only)
  - [[summaries/harness-engineering/taotian-rd-workflow-20260806]]
- Files updated (本批次):
  - index.md (新增 2 个 Summaries 条目)

## [2026-08-12] ingest | Agent 历史会话扫描/提取工具调研（kayba-ai/agentic-context-engine 赛道）
- Source: GitHub search + 各项目 README（已落 raw/articles/）
- 调研目标: 类似 kayba-ai/agentic-context-engine 的"扫描 agent 历史会话→总结出 memory、skill、wiki"项目
- 核心结论: ACE 是下游 runtime 而非上游扫描器；真正对口的扫描器是 Open Amnesia / cc-analyst / reflect-skill-claude
- 范式: 见 [[Session-Extraction-Pipeline]]（5 步：采集→标准化→聚类→提取→策展）
- 新建 7 个实体页 + 1 个概念页 + 更新 Memory-Systems.md 总览
- Files created:
  - [[entities/memory-systems/Agentic-Context-Engine-ACE]] (kayba-ai 商业引擎, 2550⭐)
  - [[entities/memory-systems/Open-Amnesia]] (vincentkoc, 30⭐, 最完整)
  - [[entities/memory-systems/cc-analyst]] (shayke-cohen, Claude/Codex JSONL→patch)
  - [[entities/memory-systems/reflect-skill-claude]] (hansvangent, 10⭐, 3-phase /reflect)
  - [[entities/memory-systems/ccflash]] (polyphilz, 7⭐, session→Anki 卡片)
  - [[entities/memory-systems/agent-memory-engine]] (uudam42, 123⭐, MCP memory server)
  - [[entities/memory-systems/Meterless]] (218⭐, local-first 上下文栈 H-MEM)
  - [[entities/memory-systems/ace-agent-ace]] (1259⭐, ACE 论文官方复现)
  - [[concepts/Session-Extraction-Pipeline]] (范式总览)
  - raw/articles/kayba-ai-agentic-context-engine.md (sha256:348dc8fdf06b2656, body-only)
  - raw/articles/vincentkoc-openamnesia.md (sha256:0feeec192a79431b)
  - raw/articles/shayke-cohen-cc-analyst.md (sha256:32e2c8c1c63af480)
  - raw/articles/hansvangent-reflect-skill-claude.md (sha256:60baa24e6063ca65)
  - raw/articles/polyphilz-ccflash.md (sha256:c0f1a0c975fff9d1)
  - raw/articles/uudam42-agent-memory-engine.md (sha256:e681dd5ba492db08)
  - raw/articles/Meterless-Meterless.md (sha256:b9a22d9f116c9350)
  - raw/articles/ace-agent-ace.md (sha256:49ade80bc5d5947a)
- Files updated (本批次):
  - [[concepts/Memory-Systems]] (总览表 + 8 行 + "相关页面"区块)
