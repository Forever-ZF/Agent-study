# Agent-study

Agent学习 —— 关于 AI 智能体生态的学习笔记与调研归档。

## 研究目录

| 主题 | 日期 | 内容 |
|---|---|---|
| [AI 智能体生态调研](./studies/2026-11-18-AI智能体生态调研/) | 2026-11-18 | 全景总览 + 完整发展史 + 现状核查 + 控制面层专章 + 问答纪要 |

### AI 智能体生态调研（2026-11-18）

- **[`全景总览.md`](./studies/2026-11-18-AI智能体生态调研/全景总览.md)** —— 全景入口。LLM → Codex → OpenClaw（小龙虾）→ WorkBuddy → dots / Grok Bot / Muse / Cowork / Claude Duet / Google Tasks，含模型层格局、横向对比、百虾大战、选型建议。**第二节"三层坐标"是全套内容的坐标系。**
- **[`发展史.md`](./studies/2026-11-18-AI智能体生态调研/发展史.md)** —— 纵轴：完整发展史。2021 的 Codex 模型 → OpenClaw 的五个名字 → Codex 的模型与 CLI 节奏 → 中国百虾大战 → 控制面层从零到 300+ → 监管从"点名警告"到"发牌管理" → 三个剧本。
- **[`现状核查.md`](./studies/2026-11-18-AI智能体生态调研/现状核查.md)** —— 横轴：现状核查。15 条断言的对错判定表、监管四层栈、四起重大安全事件、协议生态规模、用户口碑、原文档错误清单与证据缺口。
- **[`控制面层专章.md`](./studies/2026-11-18-AI智能体生态调研/控制面层专章.md)** —— 控制面层专章。站在 agent 外面、负责启动进程的那一层：六种形态、30+ 项目卡片、ACP 底座、选型建议。
- **[`问答纪要.md`](./studies/2026-11-18-AI智能体生态调研/问答纪要.md)** —— 问答纪要。五轮追问、一个被纠正三次的心智模型，记录结论是怎么推出来的。

**三个在复核中被推翻的判断**

| 原判断 | 现状 |
|---|---|
| 通用智能体控制面至今是空白 | OpenClaw 自称 "local-first control plane"，OCE 提供多租户控制面，ClawManager 等已出，Gartner 已立项首届 AMP 魔力象限 |
| WorkBuddy 覆盖超 12000 名腾讯员工 | DAU 超 1000 万、日均 Token 超 100 万亿（腾讯 Q3 财报） |
| Meta 押注"更强的隔离" | 路透 2026-11-18 证实 Muse 越权读取 Signal 加密消息——病灶是授权粒度，不是隔离强度 |

**三层速记**

| 层 | 判据 | 代表 |
|---|---|---|
| **智能体层** | 它自己就是那个干活的进程 | Codex、Claude Code、Muse Code、WorkBuddy、Grok Bot、OpenClaw、ClawBot |
| **控制面层** | **它启动进程**（spawn CLI、分配 worktree、审批流回） | T3 Code、Orca、Paseo、Paperclip、agent-autoloop、Vibe Kanban |
| **方法论层** | **它被进程加载**（SKILL.md 拷进 `~/.xxx/skills/`） | solo-to-team-skills、mcode-stack、tstack |

一句话：**T3 Code 是"派人"，solo-to-team 是"发方法论手册"。**
