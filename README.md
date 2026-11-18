# Agent-study

Agent学习 —— 关于 AI 智能体生态的学习笔记与调研归档。

## 研究目录

| 主题 | 日期 | 内容 |
|---|---|---|
| [AI 智能体生态调研](./studies/2026-11-18-ai-agent-ecosystem/) | 2026-11-18 | 从 LLM 到常驻通用智能体的全景整理 + 控制面层专章 + 问答纪要 |

### AI 智能体生态调研（2026-11-18）

- **[`ai-2026-11-overview.md`](./studies/2026-11-18-ai-agent-ecosystem/ai-2026-11-overview.md)** —— 全景入口。LLM → Codex → OpenClaw（小龙虾）→ WorkBuddy → dots / Grok Bot / Muse / Cowork，含模型层格局、横向对比、百虾大战、选型建议。**第二节"三层坐标"是全套内容的坐标系。**
- **[`agent-control-plane-landscape.md`](./studies/2026-11-18-ai-agent-ecosystem/agent-control-plane-landscape.md)** —— 控制面层专章。站在 agent 外面、负责启动进程的那一层：六种形态、30+ 项目卡片、ACP 底座、选型建议。
- **[`qa-journey.md`](./studies/2026-11-18-ai-agent-ecosystem/qa-journey.md)** —— 问答纪要。五轮追问、一个被纠正三次的心智模型，记录结论是怎么推出来的。

**三层速记**

| 层 | 判据 | 代表 |
|---|---|---|
| **智能体层** | 它自己就是那个干活的进程 | Codex、Claude Code、Muse Code、WorkBuddy、Grok Bot、OpenClaw、ClawBot |
| **控制面层** | **它启动进程**（spawn CLI、分配 worktree、审批流回） | T3 Code、Orca、Paseo、Paperclip、agent-autoloop、Vibe Kanban |
| **方法论层** | **它被进程加载**（SKILL.md 拷进 `~/.xxx/skills/`） | solo-to-team-skills、mcode-stack、tstack |

一句话：**T3 Code 是"派人"，solo-to-team 是"发方法论手册"。**
