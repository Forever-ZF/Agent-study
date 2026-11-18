# AI 智能体生态调研（2026-11-18）

本目录是 2026-11-18 一次连续调研 + 一次补充复核的成品。五份文档互相咬合，建议按下面的顺序读。

## 阅读顺序

1. **[`ai-2026-11-overview.md`](./ai-2026-11-overview.md)** —— 全景入口
   从 LLM → Codex → OpenClaw（小龙虾）→ WorkBuddy → dots / Grok Bot / Muse / Cowork / ClawBot，
   含模型层格局、横向对比、百虾大战和选型建议。
   **第二节"三层坐标"是全套内容的坐标系**，先读它。

2. **[`development-history.md`](./development-history.md)** —— 纵轴：完整发展史
   从 2021 年的 Codex 模型一路写到 2026-11 的监管栈。含 OpenClaw 的**五个名字**与完整生命周期、
   Codex 的模型与 CLI 迭代节奏、百虾大战时间线、控制面层从零到 300+ 的关键节点，
   以及**三个剧本**（分层固化 / 通用控制面补位 / 安全事故倒逼治理）。

3. **[`status-review-2026-11-18.md`](./status-review-2026-11-18.md)** —— 横轴：现状核查
   对既有资料做逐条复核：15 条断言的对错判定表、**监管四层栈**（中国 / 欧盟 / 美国）、
   **四起重大安全事件**、协议生态的真实规模、用户口碑原文摘录、原文档错误清单与**证据缺口**。
   **想快速知道"哪里过时了"就看这份的速览表。**

4. **[`agent-control-plane-landscape.md`](./agent-control-plane-landscape.md)** —— 控制面层专章
   站在 agent 外面、**负责启动进程**的那一层：六种形态、30+ 项目卡片、ACP 底座、选型建议。

5. **[`qa-journey.md`](./qa-journey.md)** —— 问答纪要（附加材料）
   这次调研的真实推演过程：五轮追问、一个被纠正三次的心智模型、以及最终收敛出的判据。
   文档是结论，这里是**怎么想到的**——适合重读结论时对照。

## 三层速记（贯穿两份文档）

| 层 | 判据 | 代表 |
|---|---|---|
| **智能体层** | 它自己就是那个干活的进程 | Codex、Claude Code、Muse Code、WorkBuddy、Grok Bot、OpenClaw、ClawBot |
| **控制面层** | **它启动进程**（spawn CLI、分配 worktree、审批流回） | T3 Code、Orca、Paseo、Paperclip、agent-autoloop、Vibe Kanban |
| **方法论层** | **它被进程加载**（SKILL.md 拷进 `~/.xxx/skills/`） | solo-to-team-skills、mcode-stack、tstack |

一句话：**T3 Code 是"派人"，solo-to-team 是"发方法论手册"。**

## 三条最容易被问倒的区分

- **Codex 能不能驱动 Claude Code / WorkBuddy？** 实践上不能，但这与分层无关，取决于有没有 ACP 适配器（官方有 `codex-acp`，OpenClaw 有 ACP agents 插件）。
- **Muse 和 Muse Code 是两个东西。** Muse 是 Meta 消费级 App（智能体层）；Muse Code 是终端编码 agent（控制面可选 provider）。
- **ClawBot 是控制面吗？** 不是。它有 9+ 个专员、独立记忆、独立订阅、分级审批，但全是自家人、跑在自研 AgentOS 上，不 spawn 第三方进程 → **智能体层 + 产品内部编排**。且同名物**至少四个**。

## 复核中修正的三个核心判断

1. **"通用智能体控制面至今是空白"** → **不成立**。OpenClaw 官方 FAQ 自称 "local-first control plane"，OpenClaw Enterprise 提供多租户控制面（OCC），ClawManager 等第三方产品已出，Gartner 已为 AI Agent Management Platforms 立项首届魔力象限。
2. **"WorkBuddy 覆盖超 12000 名腾讯员工"** → **DAU 超 1000 万、日均 Token 超 100 万亿**（腾讯 Q3 财报，2026-11-18）。差三个数量级。
3. **"Meta 押注更强的隔离"** → **已被路透 2026-11-18 证伪**。Muse 实际越权读取了 Signal 端到端加密消息——问题不在"有没有隔离"，而在"授权粒度是否与 agent 的实际需要匹配"。

## 口径与免责

- 整理时间 2026-11-18；star / fork / issue 数为 **GitHub REST API 当日实时值**（token 认证），未标注实时值的项目沿用目录站口径。
- 闭源产品的功能描述来自官网自述，未经独立验证；开源项目基于仓库元数据与官方文档。
- 四大同名 ClawBot：① clawbot-ai.app（本目录讨论的 Soul AI LLC 产品）② cloud.clawbot.ai（OpenClaw 云托管平台）③ 微信官方 ClawBot（腾讯 2026-03-22 上线的 OpenClaw 插件）④ clawbot.ai（另一个站点）。
- 查不到的写"暂缺"，不推测；冲突项并列标注。

## 来源

本目录内容来自 2026-11-18 的一次 Codex 会话（session `01a1ec73-59d2-7823-8296-030ba3992e70`）及其补充复核。
会话中尚未落盘的问答推演过程已整理为 [`qa-journey.md`](./qa-journey.md)。

