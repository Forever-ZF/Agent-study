# 控制面层全景：2026 年 11 月的 Agent Orchestration 生态

> 整理时间：2026-11-18 ｜ 口径：GitHub 元数据 + 各产品官方文档（star 数为 11/15–11/18 期间目录站抓取值，各站略有差异）
> 姊妹文档：[`ai-2026-11-overview.md`](./ai-2026-11-overview.md) —— 全景入口（模型 / Codex / OpenClaw / WorkBuddy / dots / Muse / 百虾大战）。本文只讲**控制面层**。

## 一、这一层是什么，为什么现在才爆发

**控制面层（Agent Control Plane / Orchestration Layer）= 站在智能体【外面】、负责启动 / 监控 / 审批 / 派活的那一层。**

### 先分清三层，避免混淆

判断一个东西属于哪一层，判据只有一条：**它和被它驱动的那个 agent 之间，是"启动进程"还是"被进程加载"。**

| 层级 | 判据 | 代表 |
|---|---|---|
| 智能体层 | 它自己就是那个干活的进程 | Codex、Claude Code、Muse Code、WorkBuddy、Grok Bot、OpenClaw |
| **控制面层（本文范围）** | **它启动进程**——spawn CLI、分配 worktree、把审批流回来 | T3 Code、Orca、Paseo、Paperclip、agent-autoloop、Vibe Kanban |
| 方法论层 | **它被进程加载**——SKILL.md 拷进 `~/.xxx/skills/`，由宿主自己执行 | solo-to-team-skills、mcode-stack、tstack |

**控制面层 vs 方法论层，是最容易问的一组。** 表面上都在"调度其他智能体"，实质完全不同：

| | T3 Code（控制面层） | solo-to-team-skills（方法论层） |
|---|---|---|
| 做什么 | **启动进程**：拉起 Claude Code / Codex CLI 并管理其生命周期 | **被进程加载**：把 SKILL.md 拷进各宿主 agent 的 skills 目录 |
| 谁在执行 | 被 spawn 出来的独立 agent 进程 | **宿主 agent 自己**（Codex / Claude Code / Grok…）读到 skill 后照做 |
| 跨 agent 协作 | 平台级自动（派活、审批、汇总） | **人工搬运**——你得手动把 Codex 的产出交给 WorkBuddy，再手动催它 |
| 定时 / 叫醒 | 有（cron、heartbeat、事件触发） | `hooks: {}` → **无定时、无叫醒**，只有 skill 内部步骤是自动的 |

一句话：**T3 Code 是"派人"，solo-to-team 是"发方法论手册"。**

**关于互驱**：Codex 在实践上不能直接驱动 Claude Code / WorkBuddy / DeepSeek Harness——但这跟分层无关，取决于有没有 ACP 适配器。官方有 `codex-acp`，OpenClaw 有 ACP agents 插件，适配器到位时跨层驱动就成立。

**关于 Muse**：Meta **Muse**（消费级 App）属智能体层；**Muse Code**（终端编码 agent）才是控制面可选的 provider。本文档后面提到的 Muse Code 均指后者。

它 2026 年才成气候，原因是 **harness 碎片化**：你现在手上可能同时有 Claude Code、Codex CLI、Cursor、Grok Build、OpenCode、Antigravity、Qwen Code、Kimi Code……每个都是黑盒 GUI 或终端，额度窗口各算各的，配置互不相通。

当前规模：

| 目录 | 收录量 |
|---|---|
| [agent-orchestrators.com](https://agent-orchestrators.com/) | 343 个开源工具 |
| [awesome-agent-orchestrators](https://github.com/erson/awesome-agent-orchestrators)（带活跃度指标） | 数百个，含 star / commit / license / 最近发布 |
| [openorchestrators.org](https://openorchestrators.org/) | 分治理 / 工具 / 服务 / 闭源产品四条线 |
| [awesome-agent-orchestration](https://github.com/anhermon/awesome-agent-orchestration) | 策展式地图 |
| [Agent Harness Engineering: A Survey](https://openreview.net/pdf?id=eONq7FdiHa) | 学术侧分类框架（OSAURUS 表） |

**一个重要判断**：这一层几乎全部集中在**编码**场景。因为只有编码 agent 同时具备三个清晰边界——CLI 进程、git 仓库、PR 流程。通用场景（WorkBuddy / OpenClaw / Grok Bot）的控制面至今没有成型，这正是 T3 Code 的 provider 列表里一个通用智能体都没有的原因。

值得留意的是，**通用场景的这块空白正在被"产品内部消化"而不是被控制面填补**：ClawBot（clawbot-ai.app）有 9+ 个专员、各自独立记忆、独立订阅、分级审批，看起来就是控制面；但专员全是它自家人、跑在自研 AgentOS 上，**不 spawn 任何第三方 agent 进程**。所以它是**智能体层 + 产品内部编排**，不是控制面层——它证明的是"控制面语义的需求真实"，但通用答案依然缺席。

---

## 二、六种形态

| # | 形态 | 控制什么 | 典型代表 |
|---|---|---|---|
| **A** | **多会话驾驶舱**（agent client / ADE） | 多个 agent 进程并排跑，共享终端 / diff / 审批 | Orca、T3 Code、Paseo、Moonfold、Tenfold |
| **B** | **看板式任务流** | 任务卡 → 派给 agent → worktree → review → merge | Vibe Kanban、Kandev、kanvibe、vibe-board |
| **C** | **组织治理型控制平面** | 组织架构、预算、审批队列、工单、目标对齐 | Paperclip、Omnara、Rakazo、Mission Control |
| **D** | **自动化中台**（事件驱动、常驻） | 事件 → 按规则派单 → 跑 → 通知 | agent-autoloop、clawd、AgentsMesh、LoopForge |
| **E** | **终端层**（tmux / herd） | 多 CLI 同屏、状态检测、worktree 生成 | herdr、multica、amux、OpenRig |
| **F** | **跨机器 / 大规模 fleet** | 20–100+ agent 并行、合并队列 | gastown、paperclip、Yao Agents、remote-swe-agents |

注：**框架类**（crewAI、AutoGen、LangGraph、Semantic Kernel、ruflo、OmO、OpenAI Agents SDK、Google ADK）常被混进这一层，但它们是"多 agent 协作框架"——你在代码里写编排逻辑，它们本身不是控制面产品。真正能开箱即用接管现成 agent 进程的是上面六类。

---

## 三、A 类：多会话驾驶舱（最贴近 T3 Code）

### Orca — 目前这一类的天花板

`stablyai/orca` ｜ **~127k star** ｜ MIT ｜ macOS / Windows / Linux + iOS App Store + Google Play ｜ YC 背书

- 定位：ADE（Agent Development Environment），"Run Claude Code, Codex, and any other coding agent in parallel, each in its own worktree"
- **支持的 agent 列表极长**：Claude Code、Codex、Grok、Cursor、GitHub Copilot、OpenCode、MiMo Code、Amp、Antigravity、Pi、oh-my-pi、Hermes Agent、Devin、Goose、Cline、Kilocode、Kimi、Kiro、Mistral Vibe、Qwen Code、Rovo Dev……以及任何 CLI agent
- 内置能力超出"驱动器"范畴：**Monaco 代码编辑器**、**Design Mode**（浏览器里选中页面元素直接发给 agent）、computer use、**Linear / Jira 看板**、agent 账号切换 + 用量统计、per-workspace 云 VM 配方
- 远程：Remote Orca Server 配对；移动端收到通知 → 打开会话 → 回复 → agent 直接开 PR
- 迭代极快：2026-10-21～25 五天内发了 v1.4.233 → v1.4.240

### T3 Code

`pingdotgg/t3code` ｜ **~64k star** ｜ MIT ｜ Electron + TypeScript ｜ Theo Browne / T3 Tools Inc.

- 差异化：**多端同屏**（桌面 / Web app.t3.codes / iOS / Android 显示同一批线程）、**worktree + 一键 PR**（标题、描述、更新日志自动生成，支持 draft PR 与 stacked PR）
- 远程：局域网 / Tailscale / T3 Connect（第三方 relay）；`t3 service install` 后台常驻
- 迭代信号：v0.1.8（11-10）加了 `request_secret`（agent 向你要密钥而非明文）、MCP 反向暴露、定时任务每次新会话
- 2026-11-14 Theo 演示：**用户在对话里描述界面，agent 当场改掉 T3 Code 自己的 UI**（主题 JSON + CSS，nightly 通道）

### Paseo

`getpaseo/paseo` ｜ MIT ｜ daemon 架构 ｜ 桌面 + 移动

- 原生 structured harness：Claude Code、Codex、Copilot、OpenCode、Pi、Antigravity、**Muse Code**；再加 **30+ ACP agent** 与任意自定义 CLI agent
- **有真正的编排语义**：让编程 agent 协调其他 agent，把工作分配到不同 provider 和机器；支持 **heartbeat**（定时让同一个 agent 继续任务）、agent profile（provider / model / thinking level / mode 一键复用）
- 文档里有一页标题就叫 "Orchestration"，是这一类里少见的把"agent 调度 agent"当主打功能的

### Moonfold

MIT ｜ v0.1.0-beta.1（2026-11 初）

- 跑 Codex、Claude Code、Cursor、Grok Build、OpenCode、Antigravity 等本地 CLI + 自己的登录
- Android 扫码配对
- **provider handoff**：一个 agent 撞上额度上限时，任务**转交另一个 agent 接着做**——这是很实用的差异化点

### Tenfold

Mac 原生 ｜ 商业

- 并行 Claude Code 会话，每个 pane 保持**真实 Claude Code CLI**
- 五种布局、**Inbox 集中收许可提示**、每 agent 一个 worktree、实时 code review、用量计量
- **Notch 集成**：从 MacBook 缺口处直接看 agent 并就地回话，⌘D 收起整个工作区
- 特色：**追踪哪个 agent 改了哪个文件，两个 agent 撞同一文件时告警**

### OpenHands Agent Canvas

`@openhands/agent-canvas`

- 通过 **ACP** 跑 Claude Code、Codex、Gemini CLI、Pi、OpenCode
- 云端只支持 Claude Code / Codex / Gemini CLI，Canvas 会自动隐藏另外两个

### Conductor（闭源，商业）

`conductor.build`

- Mac app 免费；**2026-11-04 起 Pro 也免费**（原本 Pro $50/月、Teams $60/人/月）
- Claude Code、Codex、OpenCode 内置，Cursor Agent 需自备 key
- **云沙箱**：每个 agent 一个 sandbox（amazon linux 2023 + node24/git/gh/jq/rg/tmux…），关掉 app 继续跑；本地模式下消息直发 provider、会话数据留在本机
- Routines：定时或 GitHub 触发 agent 运行

### Vibe Kanban

`BloopAI/vibe-kanban` ｜ Apache-2.0 ｜ Rust ｜ ~28k star

- 看板式：issue 卡 → 派给 agent → git worktree 工作区 → diff 内联评论 → 预览运行中的 app → 开 PR 合并
- 支持 10+ agent：Claude Code、Codex、Gemini CLI、Copilot、Amp、Cursor、OpenCode、Droid、Claude Code Router、Qwen Code
- **⚠️ Bloop AI 公司 2026-04-10 关停**，付费计划终止、托管云下线，转纯社区维护（`npx vibe-kanban` 本地跑）

### 其他（同一形态，量级较小）

| 项目 | 一句话 |
|---|---|
| **Kandev** (Go/Next.js) | 自托管看板 + 开发环境，ACP 编排，带 task-agent MCP（agent 可建子任务、跨仓、互相发消息） |
| **kanvibe** | 看板管理多 agent |
| **vibe-board** | 拖拽看板派活 Copilot / Claude Code / Codex / OpenCode，provider pattern 统一接口 |
| **Emdash** | 并行 agent + 任意 provider 的 agentic 开发环境 |
| **stagewise** (AGPL, ~6.8k) | agentic IDE，带 app 预览与 Git 流程 |
| **Omnigent** | meta-harness，Claude Code / Codex / Cursor / OpenCode / Hermes / Pi + 可换沙箱后端，带策略执行 |
| **Claudexor** | 本地控制面，把写操作留成 patch 待应用，跨 harness 做 best-of-N |
| **Yao Agents** | Go Yao Engine 单二进制，跨设备工作区 + 任务板 + Open API |

---

## 四、C 类：组织治理型控制平面

### Paperclip —— 概念上最激进的一个

`paperclipai/paperclip` ｜ **~114k star** ｜ 18.9k fork ｜ MIT ｜ TypeScript

- 一句话定位：**"If OpenClaw is an employee, Paperclip is the company."**
- 能力：组织架构图（org charts）、**预算**、治理、目标对齐、agent 协调
- **hire API**：agent 可以自己调用 API 招同伴 → 请求进审批队列 → 新 agent 处于 `pending_approval`，等你点头
- **agent 按 heartbeat 领工单**（在预算和组织架构约束下）
- 支持 Claude Code、Codex、Gemini CLI、Cursor、OpenCode、git worktrees、容器、桌面端、MCP、定时运行
- 两种接入：本地 CLI/session adapter（启动或恢复本地会话并跟踪）+ **fire-and-forget**（webhook/API 打给外部已经在跑的 agent）
- 官方还写了一篇《What Is AI Agent Orchestration》，把编排模式分层讲清楚

### 同类

| 项目 | 一句话 |
|---|---|
| **Omnara** (Apache-2.0, Go) | 自托管托管型 agent 平台，任意模型、任意机器，API 驱动，有定时运行 |
| **Rakazo** (~8.5k) | 持久化 AI 队友，带记忆、例程、同伴间的任务委派 |
| **Mission Control** (~6.5k) | 自托管控制面：派单到 agent runtime，追踪 run / 排程 / 花费 |
| **Open Multi-Agent** (~7.1k) | 把目标拆成 task DAG，确定性调度器跑专用 agent，带审批、trace、评估、检查点、断点续跑 |
| **AIOS** (~6.6k) | Agent OS 内核，调度 LLM / 记忆 / 存储 / 工具请求 |
| **Clawith** | 多 agent 平台，agent 持持久身份与工作区，互相委派协作 |
| **devspace** (~5.5k) | 极简 MCP coding-agent harness，给 ChatGPT / Claude / Hermes / Grok Bot / OpenClaw 用 |

---

## 五、D 类：自动化中台（事件驱动，常驻）

### agent-autoloop

`KKKKhazix/agent-autoloop` ｜ TypeScript ｜ 曾两天涨 2878 star ｜ 零外部依赖内核

- 定位："让你的命令行 Agent 在后台自己接活、自己跟进到底；平时不烦你，遇到拿不准的事才发卡片找你拍板"
- 架构：把**事件来源、Agent、通知通道、代码上线流水线全部拆成可插拔插件**
- 通知走飞书卡片（带签名校验）；已跑通 Claude Code、Codex、ACP（官方 Codex 适配器）、ZCode，事件触发 + 跨进程会话续接
- 审计做得很细：`agent-autoloop why 42` 查一轮的事件、规则和回执；`rules explain` 看生效规则和下次定时；`emit --dry-run` 预演事件会派给谁
- 安全设计值得注意：外部文字只作为资料放进文件，不拼进提示词；事件里的 `cwd` / `agent` / `access` 字段会被直接拒收
- 有 `delivery` 插件做 PR 上线流水线，中台停机时也能在原机器上处理已 deliver 的 PR

### 同类

| 项目 | 一句话 |
|---|---|
| **clawd** (~18.3k) | MIT 桌面 app，最多四个 agent 并排 + Flows（定时 / webhook / Linear / Sentry / PostHog / Vercel 事件触发） |
| **AgentsMesh** (~2.3k) | Agent Workforce Platform：远程 AI 工作站（AgentPods），PTY 沙箱 + git worktree 隔离，agent 通过 channel 与 pod 绑定协作，内置带 MR/PR 的看板 |
| **LoopForge** (Tencent) | Go 控制面：web / CLI / API / 移动 / Slack；正在做**通用 ACP 执行器**（Issue #635），接任意兼容 CLI |
| **CompozyOS** (~2.9k) | 守护进程监管 ACP agent CLI，带 session、loop、cron 触发与审批 |
| **AgentField** (~2.7k) | 控制面，对扇出到多 agent 的调用做排队、重试、链路追踪 |
| **acpx** (~3.4k) | 无头 CLI，管理跨 agent 的持久命名 ACP 会话，可脚本化多步流程 |
| **factory** | 从可信工单队列拉任务到隔离的 Codex 工作区，无人值守 |
| **remote-swe-agents** | Lambda 上的 serverless 控制面，每会话一台 EC2 worker，由 issue 评论 / 指派 / PR review 触发 |

---

## 六、E / F 类：终端层与大规模 fleet

| 项目 | 形态 | 规模 |
|---|---|---|
| **herdr** (~51.5k) | Go tmux TUI，跑 9 个 coding CLI，逐 CLI 状态检测、diff review、worktree 生成 | 中 |
| **multica** (~56.5k) | — | 中 |
| **amux** (~560★，MIT + Commons Clause) | tmux/herdr + Rust 二进制 + SQLite，coding fleet 编排器 | 中 |
| **OpenRig** (~8.9k, Apache-2.0) | YAML 定义的 agent team，Claude Code 和 Codex 作为常驻 tmux 系统 | 中 |
| **gastown** (~18.8k, Go) | 扩到 **20–30 个 agent**，git-backed issue tracking，**Bors 式合并队列** | 大 |
| **OmO** (~70.9k) | 一个 prompt 变成跨模型的并行 agent 图 | 大 |
| **Codewhale** (~41.2k, Rust) | 把一个任务拆给多个 agent，支持任意模型 | 大 |
| **buzz** (~37.5k, Apache-2.0) | Nostr relay 频道，agent 作为成员，各自有密钥和审计轨迹 | 大 |
| **oh-my-claudecode** (~40.9k) | Claude Code 插件，多 agent 自动驾驶流水线（规划 / 执行 / QA 三段） | 中 |
| **Claude Octopus** (~4.4k) | 把一个任务派给多个模型 CLI，带**共识门**与议会式审议 | 中 |
| **AgentTeams** (~6.0k, Go) | Manager-Workers 运行时，manager 编排 worker，在 Matrix 房间协作 | 中 |
| **ClawTeam** (~5.6k) | agent 自己一条命令生成队友，文件或 P2P 收件箱通信 | 中 |
| **pi-subagents** (~4.2k) | 让 Pi 委派给并行前台 / 后台子 agent | 小 |
| **zeroshot** (~1.6k) | planner + implementer + **独立验证者**在隔离环境跑，循环到验证通过或可复现失败 | 中 |
| **Ralph orchestrator** (~3k) | Hat 式循环，维护 agent 直到任务完成 | 中 |

---

## 七、底座：ACP 已经成为这一层的通用接口

**Agent Client Protocol（ACP）** = 客户端 ↔ 编程 agent 的标准通信协议（JSON-RPC 2.0），角色对标 LSP。

| 事实 | 数据 |
|---|---|
| 稳定线协议版本 | **v1**（2026-11-05） |
| ACP Registry 收录 agent | **47 个**（2026-11-13，每小时同步各 agent 新版本，每个带 `agent.json`：npx 包或分平台二进制 + sha256 + 启动参数） |
| 官方适配器 | `claude-agent-acp`（~2.8k star，v0.107.0）、`codex-acp`（v1.12.0） |
| 大厂落地 | **JetBrains 2026-09-22 发布 JetBrains Air**，明确以 ACP 作为 IDE ↔ agent harness 的连接标准；IntelliJ / AI Assistant 支持 ACP |
| 谁在吃 ACP | T3 Code、OpenHands Agent Canvas、Orca、Paseo、OpenClaw（ACP agents 插件）、goose、Zed、CompozyOS、acpx、LoopForge |

**意义**：这一层正在"协议化"，不再各自造私有适配。判断一个控制面产品的成熟度，看它支持几个 ACP agent、能否接任意 CLI。

---

## 八、五个趋势判断

1. **这一层是 2026 年最大的开源拥挤赛道之一。** 343+ 个工具、多个 100k+ star 项目（Orca 127k、Paperclip 114k、t3code 64k），但大多数是同一批想法的不同实现——worktree 隔离 + 多 provider + diff review + 一键 PR。

2. **价值正在从"能开几个 agent"往"能治理"迁移。** Paperclip 的组织架构 / 预算 / hire 审批、gastown 的 20–30 agent 合并队列、agent-autoloop 的 `why 42` 审计链——这些是控制面真正的护城河，前端 UI 反而在快速同质化。

3. **ACP 是这一层的收敛点。** 有了 ACP registry，Claude Code / Codex / Cursor / Gemini CLI / OpenCode / Pi / Qwen Code 都能被同样接进来。**建议选型时优先看 ACP 兼容性，而不是看它自称支持多少 agent。**

4. **通用场景的控制面还没形成。** 全部集中在编码，因为只有编码 agent 有清晰边界（CLI + git + PR）。WorkBuddy / OpenClaw / Grok Bot 这一侧，"控制面"至今是空白——OpenClaw 有 ACP agents 插件能跑外部 harness，方向反过来了。
   注意别把 ClawBot 这类**产品内部多角色编排**误算成控制面：它不驱动第三方进程。

5. **国内在这一层起步晚但已有动作。** 腾讯 LoopForge（Go 控制面，web/CLI/API/移动/Slack，正在做通用 ACP 执行器）、CodeBuddy 生态；国内更多精力还压在智能体层（百虾大战）和模型层。

---

## 九、选型建议

| 你的需求 | 选 |
|---|---|
| 想要最完整的桌面驾驶舱 | **Orca**（支持 agent 最多，内置编辑器 + 浏览器 Design Mode + 看板） |
| 想要多端同屏（手机审批） | **T3 Code** 或 **Paseo**（Paseo 编排语义更强，T3 Code Git 流程更顺） |
| 想让 agent 撞额度时自动接力 | **Moonfold**（provider handoff） |
| 想要看板式任务流 | **Vibe Kanban**（社区维护）、**Kandev**（自托管 + task-agent MCP） |
| 想把 agent 当公司来管（预算/审批/组织图） | **Paperclip** |
| 想做事件驱动、常驻、跨机器 | **agent-autoloop**（飞书卡片审计最细）、**LoopForge**（腾讯，国内） |
| 只用 Claude Code 且想极致并行 | **Tenfold**（Mac，Notch + 撞文件告警） |
| 要规模化到 20+ agent | **gastown**（合并队列）、**Paperclip** |
| 要通用 agent（非编码）编排 | **目前没有成熟选项**——这是这一层的真实空白 |

---

### 主要信息源

- 产品官方站与仓库：orca.dev、t3.codes、paseo.sh、paperclip.ai、moonfold、gettenfold.app、vibekanban.com、conductor.build、docs.manyfold.ai、gettenfold.app、docs.openhands.dev
- 目录与策展：agent-orchestrators.com、awesome-agent-orchestrators（erson / andyrewlee）、awesome-agent-orchestration（anhermon）、openorchestrators.org、tomrochette.com 编排特性矩阵、ShakeLv/oar-awesome-cli-coding-agents
- 协议：agentclientprotocol.com、acpserver.org、JetBrains ACP Registry 与 JetBrains Air 发布、agentclientprotocol/registry、agentclientprotocol/codex-acp、claude-agent-acp
- 学术：Agent Harness Engineering: A Survey（OpenReview）
