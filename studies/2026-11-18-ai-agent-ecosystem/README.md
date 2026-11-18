# AI 智能体生态调研（2026-11-18）

本目录是 2026-11-18 一次连续调研的成品。两份文档互相咬合，建议按下面的顺序读。

## 阅读顺序

1. **[`ai-2026-11-overview.md`](./ai-2026-11-overview.md)** —— 全景入口
   从 LLM → Codex → OpenClaw（小龙虾）→ WorkBuddy → dots / Grok Bot / Muse / Cowork / ClawBot，
   含模型层格局、横向对比、百虾大战和选型建议。
   **第二节"三层坐标"是全套内容的坐标系**，先读它。

2. **[`agent-control-plane-landscape.md`](./agent-control-plane-landscape.md)** —— 控制面层专章
   站在 agent 外面、**负责启动进程**的那一层：六种形态、30+ 项目卡片、ACP 底座、选型建议。

3. **[`qa-journey.md`](./qa-journey.md)** —— 问答纪要（附加材料）
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
- **ClawBot 是控制面吗？** 不是。它有 9+ 个专员、独立记忆、独立订阅、分级审批，但全是自家人、跑在自研 AgentOS 上，不 spawn 第三方进程 → **智能体层 + 产品内部编排**。

## 口径与免责

- 整理时间 2026-11-18；star 数为 11/15–11/18 期间目录站抓取值，各站略有差异。
- 闭源产品的功能描述来自官网自述，未经独立验证；开源项目基于仓库元数据与官方文档。
- 三大同名 ClawBot：① clawbot-ai.app（本目录讨论的 Soul AI LLC 产品）② 微信官方 ClawBot（腾讯 2026-03-22 上线的 OpenClaw 插件）③ clawbot.ai（另一个站点，安装脚本指向 openclaw.ai）。

## 来源

本目录内容来自 2026-11-18 的一次 Codex 会话（session `01a1ec73-59d2-7823-8296-030ba3992e70`）。
会话中尚未落盘的问答推演过程已整理为 [`qa-journey.md`](./qa-journey.md)。

