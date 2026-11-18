# AI 发展现状整理：从 LLM 到常驻通用智能体

> 整理时间：2026-11-18 ｜ 口径：公开信息 + 一手文档
> 姊妹文档：[`agent-control-plane-landscape.md`](./agent-control-plane-landscape.md) —— 控制面层专章。本文是全景入口，"谁在 spawn 谁"看那一份。

## 一、一句话概括

过去三年 AI 的主线不是"模型越来越大"，而是 **AI 从"回答你"变成"替你做"，再变成"自己找活干"**。

| 阶段 | 形态 | 代表 | 核心变化 |
|---|---|---|---|
| ① 对话期 | 大语言模型（LLM） | GPT-4 / Claude / Gemini / DeepSeek | 你问，它答 |
| ② 垂直智能体 | 编程智能体 | Codex / Claude Code | 在一个明确领域里能自己动手 |
| ③ 通用外壳 | 开源个人智能体 | **OpenClaw（小龙虾）** | 任何领域，本地跑，权限极大 |
| ④ 常驻通用智能体 | 云端 AI 同事 | **dots / Grok Bot / Muse / Cowork / WorkBuddy** | 全天在线，主动工作，多任务并行 |

2026 年的核心变化是：**② → ③ → ④ 在半年内同时发生**。Codex 从"只写代码"长成了通用工作智能体的内核（ChatGPT Work 就是它的泛化版），OpenClaw 把"个人智能体"从概念做成了开源爆款，而 dots / Grok Bot / Muse 则把"AI 同事"做成了各家标配的云端产品。

> 上表 ①–④ 是**时间阶段**。下一节的"智能体层 / 控制面层 / 方法论层"是**架构分层**，两套编号不是一回事。

---

## 二、三层坐标：判断一个项目属于哪一层

最常见的混乱，是把"**控制别人的 agent**"、"**给 agent 发方法论**"、"**自己就是 agent**"混成一类。三者的技术分界线其实非常清楚：

| 层级 | 判据 | 代表 |
|---|---|---|
| **智能体层** | 它自己就是那个干活的进程 | Codex、Claude Code、Muse Code、WorkBuddy、Grok Bot、DeepSeek Harness、OpenClaw |
| **控制面层** | **它启动进程**——spawn CLI、分配 worktree、把审批流回来 | T3 Code、Orca、Paseo、Paperclip、agent-autoloop、Vibe Kanban |
| **方法论层** | **它被进程加载**——SKILL.md 拷进 `~/.xxx/skills/`，由宿主自己执行 | solo-to-team-skills、mcode-stack、tstack |

**三个最容易踩的坑**

1. **"能不能互驱"取决于有没有 ACP 适配器，不取决于分层。** Codex 在实践上不能直接驱动 Claude Code / WorkBuddy / DeepSeek Harness；但官方有 `codex-acp`，OpenClaw 有 ACP agents 插件——适配器到位，跨层驱动才成立。
2. **控制面层 ≠ 方法论层。** T3 Code 真的 spawn 出别的 agent 进程；solo-to-team-skills 只是把 skill 拷进 Codex / Claude Code / Grok 的目录，**跨 agent 搬运、催办、验收仍是人工的**，自动的只有 skill 内部步骤，且 `hooks: {}` 意味着没有定时叫醒。
3. **同一家不同产品可以分属不同层。** Meta **Muse**（消费级 App）属智能体层；**Muse Code**（终端编码 agent）才是控制面层可选的 provider。名字像，层不同。

**一个反向的空白**：ClawBot（clawbot-ai.app）看起来像控制面——9 个专员、各自独立记忆、独立订阅、分级审批——但专员全是自家人、跑在自研 AgentOS 上，不 spawn 任何第三方 agent 进程，所以它是**智能体层 + 产品内部角色编排**。它更像是把控制面语义闭源内化进了单个产品，而不是提供了通用控制面。（同名物另有两个：微信官方 ClawBot 是 OpenClaw 的微信插件；clawbot.ai 是另一个站点。）

---

## 三、模型层：2026 年 11 月的底座格局

模型已经不是瓶颈，**模型正在"商品化"**，真正的分化发生在模型之上。

### 海外第一梯队

| 厂商 | 旗舰 | 时间 | 特点 |
|---|---|---|---|
| OpenAI | **GPT-6.2 Astra / GPT-6.2 Sol** | 2026-11-17 | 前天刚发。专业产出、编码、Computer Use 全面提升；API 价降 20%、缓存降 60% |
| OpenAI | GPT-6.1 Sol / Terra | 2026-10-26 | Sol 日常主力，Terra 性价比档 |
| Anthropic | **Claude Fable 5.2 / Mythos 5.2** | 2026-11-09 | Anthropic 已上市；Fable 5.2 综合基准第一 |
| Anthropic | Claude Opus 5.5 | 2026-09-22 | 日常最强驱动，比 Opus 5 便宜 40% |
| Google | Gemini 4 Argon / Carbon / Hydrogen | 2026 | 综合分第一（Argon）；Hydrogen 为速度档 |
| xAI | Grok 4.20 | 2026-04 | 多智能体推理强；xAI 已并入 SpaceX |

### 国产第一梯队（多为开源权重）

| 厂商 | 旗舰 | 时间 | 备注 |
|---|---|---|---|
| DeepSeek | V4-Pro / **V4.1-Flash** | 2026-09-10 | 552B MoE，多项基准反超自家 Pro；原生视觉 |
| 月之暗面 | **Kimi K3 / K3.1 Pro** | 权重 7-27，Pro 10-26 | 2.8T MoE，1M 上下文，KDA 架构；长程 Agent 强化 |
| 智谱 | GLM-5.3 / 5.3-Flash | 2026-11 | 云端 + 端侧双线 |
| 阿里 | Qwen3.5–3.8 | 持续 | Apache 协议，全球下载量领先的开源家族 |
| MiniMax | M3 / M3.1 | 2026 | 多模态强 |
| 腾讯 | 混元 A13B | 持续 | WorkBuddy 默认底座之一 |
| Meta | Muse Spark 1.3 | 2026-11 | 专供 Muse 智能体 |

**关键判断**：国产开源模型已经是全球第一梯队，不再是"性价比替代品"。这直接导致 OpenClaw / WorkBuddy 这类产品**不需要自己训模型**——它们的价值全在"外壳 + 权限 + 生态"。

---

## 四、Codex：从"写代码"到"用代码把活干完"

Codex 是这条技术路线上**最早也最成功的垂直智能体**，也是理解后面所有通用智能体的原型。

| 时间 | 变化 |
|---|---|
| 2025 | Codex CLI 出现，终端里的编程 agent：读写文件、跑命令、改仓库 |
| 2026-03-04 | **Codex app** 桌面端上线（Windows / Mac） |
| 2026-04-16 | **Computer Use**：看屏、点按、打字，接管桌面任意 GUI 应用；macOS 后台跑。官方定调"Codex 不再局限于笔记本电脑"（Codex Has Left The Laptop） |
| 2026-05-21 | Locked / Remote：Mac 锁屏后继续工作，可从手机远程触发 |
| 2026-05-29 | Windows Computer Use（前台接管，与 macOS 后台模式体感差别明显） |
| 2026-07-09 | **ChatGPT Work** 发布：ChatGPT 对话 + Codex 合并成 super app，做文档 / PPT / 网站 / 数据分析 |
| 2026-09-29 | **DevDay 2026**：20+ 项发布，含 dots、ChatGPT Space、Bedrock Managed Agents、Codex Security Cloud |

**Codex 的能力四件套**

1. **Codex Cloud** — 跑在 OpenAI 托管的机器上，合上笔记本任务继续；每个 repo 有可复用环境
2. **Computer Use** — 从命令行扩展到 GUI，覆盖所有桌面 App
3. **多智能体工作流** — worktree 隔离、多仓库并行、PR 审查
4. **Hooks / 插件 / Apps SDK** — 可在提示里插扫描器、验证器、记忆写入；插件生态开放

**Codex 的真正意义**：它证明了一件事——**"能安全地操作真实电脑 + 能连续工作很多小时"这两件事一旦成立，编程就不再是一个特殊场景，而只是一个恰好好写的场景。** 于是有了 ChatGPT Work（泛化到办公），也直接催生了后面所有"通用智能体"。

---

## 五、OpenClaw（小龙虾）：现象级开源个人智能体

**是什么**：奥地利独立开发者 Peter Steinberger 的项目。2025 年 11 月作为周末项目发布，初名 Clawdbot → 2026-01-27 因商标纠纷改名 Moltbot → 2026-01-30 定名 OpenClaw。因红色机械龙虾图标，被中国网友称为"小龙虾"。

**有多火**

- GitHub 约 **38.8 万 star / 8.1 万 fork**（截至 2026-08-26）
- 24 小时内破 9000 star，两个月破 10 万
- Mac Mini 一度卖断货；国内催生"付费安装""有偿部署""养虾"服务
- 2026-02 创始人加入 OpenAI，**项目转入 OpenClaw 基金会**（非营利）由 OpenAI 继续支持；技术决策仍由 Steinberger 主导
- 2026 年初国内爆发"**百虾大战**"：20+ 家科技公司跟进

**技术架构**（理解它的关键）

```
消息平台（微信/飞书/钉钉/企微/QQ/Telegram/Slack/WhatsApp…）
        │  WebSocket
        ▼
Gateway（网关，127.0.0.1:18789）—— 唯一对外控制平面
        │  消息路由 / 会话管理 / 访问控制 / 工具执行权限边界
        ▼
Agent（智能体）—— 独立人格（SOUL.md）+ 上下文 + 工具集
        │  Agentic Loop：组装上下文 → 模型推理 → 调工具 → 反馈 → 再推理 → 写记忆
        ├──► Skills（技能包）—— SKILL.md 纯自然语言描述，支持嵌套调用
        └──► Memory（记忆）—— MEMORY.md + memory/YYYY-MM-DD.md，混合检索
```

**为什么它重要**

1. **证明"个人级全权限智能体"可行**：不是云端沙箱里的助手，而是跑在自己机器上、直接用自己的身份和权限干活
2. **Skills 机制成了事实标准**：SKILL.md 用纯 Markdown 描述"遇到某类任务怎么做"，可嵌套、可上架（ClawHub）、可跨产品复用
3. **MCP 成为通用扩展协议**：OpenClaw 原生适配 MCP，也兼容 Claude 的 Markdown 命令文件
4. **把"AI 助手"从工具变成了"数字员工"**：养虾的隐喻一夜传开

**它也把风险推到了台面上**（2026 上半年国内舆论主线）

- 2026-02-05 工信部相关平台发布 OpenClaw 安全风险预警
- 2026-03-11 工信部 NVDB 发布"**六要六不要**"建议
- **"龙虾悖论"**：想让它干的事越多，给它的权限必须越大；权限越大，风险越高
- 具体风险：账户资金被悄悄转走、电脑被远程控制、多年工作文件被清空；供应链投毒（恶意 Skill 包 / 插件）
- 官方也曾提示"审慎使用"

---

## 六、WorkBuddy：腾讯的"腾讯版 OpenClaw"

**是什么**：腾讯推出的**全场景职场 AI 智能体桌面工作台**，业内普遍称其为"腾讯版 OpenClaw"。2026-01-19 启动腾讯内部测试，目前是超 **12000 名腾讯员工**的常态化 AI 办公工作台。2026-10-30 腾讯宣布全面开放 WorkBuddy 平台能力。

**定位**：不是给技术人员的复杂工具，而是面向**普通职场人**的 AI 工作台。用户在腾讯生态（QQ / 企微）里下达任务，它在本地终端自主规划并执行多模态办公任务。

**核心能力**

- 单条自然语言指令 → 自主规划 → 执行 → 交付可验收成果（文档 / 表格 / PPT / 图表 / 报告）
- **本地文件实操**：基于授权的本地文件夹读取权限，在本地终端真实执行
- **Claw 远程控制**：微信 / 企微 / QQ / 飞书 / 钉钉发一条消息，就能远程调度电脑上的 WorkBuddy 干活
- **多智能体并行**：如软件开发场景，产品经理定需求 → 架构师拆任务 → 工程师实现 → QA 验证
- **模型切换**：内置混元 / DeepSeek / GLM / Kimi / MiniMax 等按场景选（混元日常快、DeepSeek 复杂推理、GLM 代码、Kimi 长文创意）
- **MCP Server 适配 + Skills 技能包扩展 + 高危指令拦截**
- 专家中心（8 位专家角色）、连接器（QQ邮箱 / 腾讯乐享 / 腾讯文档）、自动化定时任务（每天 9 点日报、每周一抓竞品）

**价格**（2026 公测期）：免费 5000 积分 / 基础 19 元月（5 万积分，10 个自定义 Skill）/ 专业 49 元月（20 万积分，30 个 Skill）/ 企业面议。

**和 OpenClaw 的关系**：**同一套技术范式，不同的商业与安全取向。**

- OpenClaw = 开源、你自己装、权限最大、零成本但风险自负
- WorkBuddy = 闭源商业产品、腾讯生态原生、开箱即用、积分制计费、自带高危拦截与沙箱

---

## 七、dots / Grok Bot / Muse / Cowork：云端"AI 同事"四强

2026 年下半年，四大厂几乎同时推出了形态高度相似的**全天候通用智能体**。这是"通用智能体元年"的标志。

### OpenAI **dots**（2026-09-29 DevDay 发布）

- 由 **GPT-6 Astra** 驱动（11-03 起加入 GPT-6.1 Terra 供不同环节调用；11-17 起为 GPT-6.2 Astra / Sol）
- **自己的云电脑**，可随时打开检查它的工作；也可授权它连接并使用你的笔记本
- **4000+ 插件**连接应用
- **主动研究（proactive research）**：你不找它时，它用**只读**工具访问已连接应用找事做——只读工具不能发消息、不能改内容、不能控制浏览器
- **安全三层**：内置防护（云电脑隔离、登录网站时模型看不到密码、后台监控可暂停）→ 访问与权限控制（自定义规则：允许 / 需批准 / 禁止）→ **auto-review**（影响账户或分享信息的操作自动审查，改密码等敏感任务始终要人来做）
- **专家型 dot**：给每个 dot 配独立身份、凭据、系统访问权限，在组织内承担专门职责
- 入口：ChatGPT / Slack / Teams / 语音通话

### xAI **Grok Bot**（2026-08-11 beta）

- 形态：**多个具名 Bot 组成一个 AI 团队**，共用一台持久云电脑（共享浏览器、文件、登录状态）
- 每个 Bot 有名字、职责、**长期记忆**
- 能登录网站、操作应用、互相交接工作；敏感动作弹审批卡
- 可按**排程或事件**自动执行例行任务
- 与 @grok 聊天机器人的区别：后者在 X 上答题，前者在云端电脑里真的干活
- 2026-10 开放给所有 Enterprise 团队

### Meta **Muse**（2026-09-08，美国先行）

- 由 **Muse Spark 1.3** 驱动；可用 iOS / Android / 网页 / WhatsApp，后续接入 Instagram、Facebook、AI 眼镜
- 跑在**专门打造的云端虚拟机**上，设计重心在"**绝不允许它触及什么**"
- **Muse Secure VM**（连 Meta 都看不到你的内容）；**Muse Confidential VM** 即将推出
- 敏感操作前与用户**多次确认**；记录已完成操作与计划任务；不与广告系统共享数据
- 用户可设名字头像、调整沟通方式，可给它"大胆的人生目标"（"减掉啤酒肚"），它会记住并主动建议
- 免费 + $20 / $100 付费档
- 2026-09-20 起亚马逊提示 Muse 未经授权使用违反其条款（Meta 回应称 Muse 看不到密码和支付信息）——**说明它确实在代替人做交易类动作，这是通用智能体真正要跨的坎**

### Anthropic **Claude Cowork**（2026-01 research preview → 2026-09-16 并入 claude.ai）

- **与 Claude Code 同一套智能体架构**，只是去掉了终端 → 把编码能力扩展到知识工作
- 跑在 Anthropic 服务器的**隔离环境**里
- 2026-08-26 内置浏览器（在自己的侧边栏开浏览器填表单，不抢你正在用的浏览器）
- 可从手机发起任务，**合上笔记本仍在云端继续**
- 2026-09-16 起"Cowork 就是 Claude"，chat 与 agentic work 合为一个应用
- Anthropic 招股书：Cowork 采用速度超过同期 Claude Code；他们把自主运行所需的 harness（安全环境、凭据、记忆、编排循环）定义为产品的一部分

### ClawBot（闭源，面向"一人公司"）

Soul AI LLC 的消费级产品，底座是自研 AgentOS 运行时：9+ 个专员岗位（研究、编程、内容、广告投放、增长、产品、交易、微信内测、电脑操作内测），每个专员独立记忆、独立订阅、可单独退掉；私有沙箱容器，转账 / 公开发布 / 跑代码等关键操作需人工审批，Pro 以上有后台自主运行的"心跳"。

**它是智能体层，不是控制面层**——专员全是自家人、跑在自研 AgentOS 上，不 spawn 第三方 agent。更准确地说，它把"角色分工 + 独立记忆 + 分级审批"这套控制面语义**闭源内化进了单个产品**，没有提供通用控制面。

### 顺带一提

- **Manus**：2026-09-01 脱离 Meta 恢复独立运营（原团队回归），2026-09-28 发布 **Manus 2.0**，定位"通用 AI 智能体实验室"
- **Microsoft Copilot Tasks**：微软的对应位

---

## 八、横向对比

| 维度 | Codex / ChatGPT Work | OpenClaw（小龙虾） | WorkBuddy | dots | Grok Bot | Muse |
|---|---|---|---|---|---|---|
| 归属 | OpenAI | 开源基金会（原作者在 OpenAI） | 腾讯 | OpenAI | xAI（SpaceX） | Meta |
| 形态 | 桌面 App / CLI / 云 | 自装在本地机器 | 桌面工作台 | ChatGPT 内的 dot | 云端 Bot 团队 | App / 眼镜 |
| 执行环境 | 本机 + OpenAI 云端 + 可授权本机 | **本地全权限** | **本地终端** | **专属云电脑** | 持久云电脑 | 专用安全 VM |
| 模型 | GPT-6.2 Astra / Sol 等（绑定） | **可自选任意 LLM** | 可切换混元/DS/GLM/Kimi/MM | 绑定 GPT-6.2 | 绑定 Grok | 绑定 Muse Spark |
| 主动性 | 事件触发、后台持续 | 定时任务、IM 遥控 | 定时任务、IM 遥控 | **主动研究（只读）** | 排程 / 事件自动跑 | 主动建议 |
| 权限哲学 | 沙箱 + 审批 + 自定义规则 | **权限最大，风险自负** | 沙箱 + 高危拦截 | 独立云机 + auto-review | 审批卡 | **安全 VM，多重确认** |
| 开闭源 | 闭源 | **开源** | 闭源 | 闭源 | 闭源 | 闭源 |
| 最大差异点 | 代码 → 通用工作的内核 | 开源 + 可换模型 + 生态 | 办公场景 + 腾讯生态 | 云端常驻 + 插件生态 | 多 Bot 分工 | 隐私叙事 + 社交入口 |

**一句话区分**

- **Codex**：一个"干活的编程同事"，现在能操作你的电脑
- **小龙虾**：一个"你自己搭的、权限全开的、什么模型都能塞进去的"通用管家
- **WorkBuddy**：一个"腾讯生态里的、小白也能用的办公版小龙虾"
- **dots**：一个"云端 24 小时不睡、还住进了你 4000 个应用里的"AI 同事
- **Grok Bot**：一个"多个具名 Bot 组队，在云端电脑里自己分工交接"的 AI 团队
- **Muse**：一个"跑在 Meta 安全虚拟机里、连 Meta 自己都看不到你内容"的私人智能体
- **ClawBot**：一个"把一整个 AI 团队打包成订阅制专员列表"的垂直一人公司工具
- **控制面层**（T3 Code / Orca / Paperclip…）：站在 agent 外面、**替 agent 启动 agent** 并管审批与 worktree 的那一层 —— 详见姊妹文档

---

## 九、国内"百虾大战"全景

2026 年初国内 20+ 家公司跟进 OpenClaw，实质是**入口之争**：谁能成为用户调用 AI 能力的默认界面。

| 公司 | 产品 / 方案 | 类型 |
|---|---|---|
| 腾讯 | WorkBuddy + QClaw + 龙虾管家（AI 安全沙箱）+ 腾讯云部署 + SkillHub | 本地 + 托管 + 安全 |
| 字节 | ArkClaw（飞书适配 + 火山引擎云部署） | 托管 + 云 |
| 阿里 | 阿里云一键部署 + CoPaw（钉钉原生）+ QoderWork + JVSClaw（移动端）+ AgentBay | 云 + 桌面 + 移动 |
| 百度 | 千帆一键体验 + DuClaw（零部署托管）+ 红手指 Operator（全球首个移动端 OpenClaw） | 云 + 托管 + 移动 |
| 华为 | 华为云一键部署 + 小艺 Claw（HarmonyOS 内置） | 云 + 移动 |
| 智谱 | AutoClaw（桌面客户端，下载即用） | 桌面 |
| 月之暗面 | Kimi Claw（托管） | 托管 |
| MiniMax | MaxClaw（托管 + 移动 Web） | 托管 + 移动 |
| 网易有道 | LobsterAI（MIT 全开源桌面 + 技能商店 + MCP 市场） | 桌面 |
| 猎豹移动 | EasyClaw（一键安装） | 桌面 |
| 科大讯飞 | AstronClaw（10000+ Skills，火星 X2） | 云 + 托管 |
| 小米 | Xiaomi miclaw（系统级手机 Agent，MiMo 模型） | 移动（内测） |
| 京东 | 京东云一键部署 | 云 |
| 当贝 / 万得 / 美团 / 360 | Molili / WindClaw / 小美 / 纳米 AI | 垂直 + 消费 |

**开源替代品**（不想被生态绑定的可选）：nanobot（港科大，代码量仅 OpenClaw 1%）、IronClaw（Rust，WASM 沙盒零遥测）、NanoClaw（容器沙盒）、CoPaw（钉钉原生）、HiClaw（Manager-Worker 多智能体）、ChatClaw（Go，30MB，9 大 IM 渠道）、PicoClaw（10MB，旧手机 / 树莓派）、MimiClaw（ESP32 裸机）。

---

## 十、几个真问题

1. **"龙虾悖论"还没解开**：能力与权限同增，收益与风险同增。OpenClaw 的 CVE 与供应链投毒、WorkBuddy 的高危拦截、Muse 的 Secure VM、dots 的 auto-review——各家答案不同，问题一样。这大概是未来两年最重要的工程议题：**如何在保持 agent 能力的同时，不把系统权限一次性交出去。**

2. **模型在贬值，模型之上的东西在升值**。国产开源模型已是全球第一梯队且免费可自部署，"接哪个模型"正在变成可替换的技术细节（只有 OpenClaw / WorkBuddy 这类把模型当插槽的产品才吃到这个红利）。真正的护城河变成：权限模型、记忆、Skill 生态、连接器，以及**入口**。

3. **"主动性"是新的分水岭**。从"你问我答"到"我主动找活干"（dots 的主动研究、Grok Bot 的排程执行、Muse 的主动建议），是 2026 下半年最重要的产品分界线。但主动性与安全直接冲突——所以 Meta 把答案押在"更强的隔离"，OpenAI 押在"只读工具 + auto-review + 自定义规则"，而不是给更多权限。

4. **形态在收敛**：本地全权（OpenClaw）vs 云端隔离（dots / Muse / Cowork）本是两条路，现在正互相靠拢——dots 可以连接你的笔记本；Codex Computer Use 已能在本机接管 GUI。**"云端跑 + 可授权触达本机"很可能是终局形态。**

5. **商业化已经分层**：闭源订阅（ChatGPT / Work / Grok / Muse）、开源免费 + 自付（OpenClaw）、托管 SaaS（Claw 生态一堆 10–300 元/月）、积分制（WorkBuddy 19–49 元/月）。Token 成本不再是用户第一关心，"它能不能真把活干完"才是。

6. **国内特殊约束**：监管对 OpenClaw 类高权限智能体明确收紧（工信部预警、六要六不要、各网信办提示），国内厂商普遍加了一套"沙箱 + 拦截 + 加密"组合拳（如华为小艺 Claw 的八维防护、腾讯龙虾管家）。出海版本因此普遍独立成 App（WorkBuddy 国际版、EasyClaw 国际版）。

---

## 十一、如果要选

| 你是 | 建议 |
|---|---|
| 开发者，要写代码 | **Codex**（本地 + Cloud 并用），可配 Claude Code |
| 极客，要完全控制 + 可换模型 + 不被绑 | **OpenClaw**（或 nanobot / IronClaw 等精简版），但务必配沙箱 |
| 普通职场人，办公文档为主 | **WorkBuddy**（微信生态 + 开箱即用）或 ChatGPT Work |
| 想体验"AI 同事"，要云端常驻 + 多应用 | **dots**（OpenAI 生态）或 **Grok Bot** |
| 高度重视隐私 | **Muse**（Secure VM，连 Meta 都看不到）或 Claude Cowork（隔离环境） |
| 国内环境，要合规 + 办公 + IM 遥控 | **WorkBuddy**（企微 / 飞书 / 钉钉），或 ArkClaw / Kimi Claw |
| 想知道前沿 | 关注 Agent 基准：ClawWork（220 GDPVal 任务 / 44 职业）、WildClawBench（InternLM） |
| 想同时驱动多个 agent（而不是自己干活） | 见 `agent-control-plane-landscape.md`：T3 Code / Orca / Paseo（驾驶舱）、Paperclip（治理）、Vibe Kanban（看板） |

---

### 主要信息源

- OpenAI：dot 正式登场、DevDay 2026 回顾、GPT-6.2 Astra and Sol、Codex 官方站与帮助中心
- Anthropic：Claude Cowork、Claude Platform 模型文档、Opus 5.5 / Fable 5.2 发布、Anthropic 招股书
- xAI：Grok Bot、x.ai/news
- Meta：Muse 发布、Meta Superintelligence Labs 研究报告、Muse Spark 1.3 评估报告、Q3 2026 财报电话会
- 腾讯：WorkBuddy 官方文档与定价页、CodeBuddy 文档
- OpenClaw：docs.openclaw.ai、GitHub Blog 维护者访谈、open-claw.me 版本历史
- 官方与监管：新华社、央视网、工信部 NVDB"六要六不要"、江苏网信、蚂蚁安全研究报告
- 媒体：Reuters、The Verge、Axios、财新、IT之家、澎湃
