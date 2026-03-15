# OpenClaw 安装与使用指南

> OpenClaw 是一款运行在你自己设备上的开源个人 AI 助手。它不只是聊天工具，而是一个**代理式 AI（Agentic AI）**——它能真正执行任务：清理收件箱、发送邮件、管理日历、审查代码、自动化浏览器操作等。

- 官网：[openclaw.ai](https://openclaw.ai)
- GitHub：[github.com/openclaw/openclaw](https://github.com/openclaw/openclaw)
- 官方文档：[docs.openclaw.ai](https://docs.openclaw.ai)

---

## 目录

1. [项目简介](#项目简介)
2. [核心特性](#核心特性)
3. [环境要求](#环境要求)
4. [安装步骤](#安装步骤)
5. [初始化配置（Onboard 向导）](#初始化配置)
6. [Telegram 配对授权](#telegram-配对授权)
7. [基本使用](#基本使用)
8. [多 Agent 管理](#多-agent-管理)
9. [技能扩展（Skills & ClawHub）](#技能扩展)
10. [Hooks 自动化](#hooks-自动化)
11. [作为后台服务运行](#作为后台服务运行)
12. [环境变量参考](#环境变量参考)
13. [常见问题](#常见问题)
14. [延伸阅读](#延伸阅读)

---

## 项目简介

OpenClaw 由 PSPDFKit 创始人 Peter Steinberger 于 2025 年 11 月发起（前身为 Clawdbot、Moltbot），于 2026 年 1 月正式更名为 OpenClaw。

与 ChatGPT 等纯对话工具不同，OpenClaw 的核心理念是**消息优先 + 本地运行 + 持续在线**：

- 通过 Telegram、WhatsApp、Slack、Discord 等消息平台与你交互
- 技能（Skills）可像手机 App 一样按需安装
- 对话记忆以 Markdown 文件形式持久化存储在本地磁盘
- 支持定时任务，能主动推送通知或执行自动化流程

---

## 核心特性

| 特性 | 说明 |
|------|------|
| 消息优先接口 | 通过 WhatsApp、Telegram、Slack、Discord 等收发指令 |
| 真实动作执行 | 清理收件箱、发送邮件、日历管理、脚本自动化 |
| 持久记忆与上下文 | 以 Markdown 文件存储在本地磁盘，隐私可控 |
| 主动自动化 | 支持定时任务，可定期执行任务或推送提醒 |
| 技能生态 | 内置 50+ 技能，ClawHub 上有 3000+ 社区技能 |
| 跨平台 | 支持 macOS、Linux、Windows（WSL2） |
| 本地优先 | 数据不经第三方服务器，完全自托管 |

---

## 环境要求

| 依赖 | 最低版本 | 说明 |
|------|----------|------|
| Node.js | **22+** | 必须，推荐使用 LTS 版本 |
| 包管理器 | npm / pnpm / bun | 任选其一 |
| AI Provider API Key | — | 支持 20+ 平台，见下方完整列表 |
| Windows 用户 | WSL2 | 强烈建议在 WSL2 环境下运行 |

### 安装 Node.js（如尚未安装）

```bash
# 使用 nvm（推荐）
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
nvm install 22
nvm use 22

# 或使用 Homebrew（macOS）
brew install node@22
```

---

## 安装步骤

### 方式一：npm 全局安装（推荐）

```bash
npm install -g openclaw@latest
```

### 方式二：pnpm

```bash
pnpm add -g openclaw@latest
```

### 方式三：bun

```bash
bun add -g openclaw
```

### 验证安装

```bash
openclaw --version
```

---

## 初始化配置

安装完成后，运行 Onboard 向导完成首次配置（约 5-10 分钟）：

```bash
openclaw onboard --install-daemon
```

> `--install-daemon` 会在向导完成后自动将 OpenClaw 注册为系统守护进程（macOS 使用 launchd，Linux 使用 systemd），实现开机自启、后台持续运行。

向导将引导你完成以下配置：

1. **选择 AI Provider**：从以下 20+ 平台中选择一个：

   | Provider | 备注 |
   |----------|------|
   | OpenAI | GPT 系列 |
   | Anthropic | Claude 系列（公司团队账号） |
   | Moonshot AI (Kimi K2.5) | Kimi K2.5 + Kimi Coding |
   | Google | Gemini 系列 |
   | xAI (Grok) | Grok 系列 |
   | Mistral AI | — |
   | MiniMax | — |
   | Qwen | 通义千问 |
   | Volcano Engine | 火山引擎 |
   | BytePlus | — |
   | OpenRouter | 聚合多家模型 |
   | Chutes | — |
   | vLLM | 本地部署 |
   | Kilo Gateway | — |
   | Z.AI | — |
   | Qianfan | 百度千帆 |
   | Copilot | — |
   | Together AI | — |
   | Hugging Face | — |
   | Venice AI | — |
   | LiteLLM | 本地多模型代理 |
   | Cloudflare AI Gateway | — |
   | Vercel AI Gateway | — |
   | Xiaomi | — |
   | Synthetic | — |
   | OpenCode Zen | — |
   | Custom Provider | 自定义端点 |

2. **输入 API Key**：粘贴对应平台的 API Key

3. **配置网络搜索（Web Search）**（可选）：让 Agent 能实时联网查询信息

   | Provider | 特点 |
   |----------|------|
   | Perplexity Search | 结构化结果，支持域名/语言/时效过滤，搜索质量高 |
   | Brave Search | 独立搜索引擎，隐私友好 |
   | Gemini (Google Search) | 接入 Google 搜索，结果全面 |
   | Grok (xAI) | 适合实时信息查询 |
   | Kimi (Moonshot) | 若已有 Moonshot API Key 可直接复用 |

   > 若已选择 Moonshot AI 作为 AI Provider，Web Search 选择 **Kimi (Moonshot)** 可复用同一个 API Key，无需额外申请。

4. **配置消息渠道（Gateway）**：选择 Telegram、WhatsApp、Slack 等

5. **配置技能（Skills）**：

   Onboard 时会扫描本地技能状态，例如：
   ```
   Eligible: 4          # 可直接安装
   Missing requirements: 47  # 缺少依赖（通常需要额外 API Key）
   ```

   以下技能需要额外配置对应的 API Key 才能启用：

   | 技能 | 所需环境变量 | 说明 |
   |------|-------------|------|
   | `goplaces` | `GOOGLE_PLACES_API_KEY` | Google 地点搜索 |
   | `nano-banana-pro` | `GEMINI_API_KEY` | Gemini 模型相关功能 |
   | `notion` | `NOTION_API_KEY` | Notion 文档操作 |
   | `openai-image-gen` | `OPENAI_API_KEY` | AI 图片生成 |
   | `openai-whisper-api` | `OPENAI_API_KEY` | 语音转文字 |
   | `sag` | `ELEVENLABS_API_KEY` | 文字转语音 |

6. **配置 Hooks（自动化钩子）**（可选）：在 Agent 执行特定命令时自动触发操作，详见 [Hooks 自动化](#hooks-自动化)

7. **选择启动方式（Hatch）**：

   | 选项 | 说明 |
   |------|------|
   | Hatch in TUI（推荐） | 在终端界面运行，轻量快速 |
   | Open the Web UI | 打开浏览器图形界面，更直观 |
   | Do this later | 跳过，稍后手动启动 |

8. **设置工作区路径（Workspace）**：本地存储技能和记忆文件的目录

> 建议至少配置一个**备用模型（Fallback Model）**，防止主模型达到速率限制时中断服务。

---

## Telegram 配对授权

首次在 Telegram 中向 Bot 发送消息时，Bot 会回复如下内容：

```
OpenClaw: access not configured.

Your Telegram user id: <你的用户ID>

Pairing code: XXXXXXXX

Ask the bot owner to approve with:
openclaw pairing approve telegram XXXXXXXX
```

此时需要在本机终端执行授权命令：

```bash
openclaw pairing approve telegram <配对码>
```

授权成功后即可在 Telegram 中正常与 OpenClaw 对话，下达任务指令。

> 每个新用户首次使用都需要 Bot 所有者执行一次授权，这是安全机制，防止陌生人使用你的 Bot。

---

## 基本使用

### 启动 OpenClaw

```bash
openclaw start
```

### 停止服务

```bash
openclaw stop
```

### 查看运行状态

```bash
openclaw status
```

### 通过消息渠道交互

配置好 Telegram/Slack 等渠道后，直接在对应 App 中向 OpenClaw 发送消息即可。示例指令：

```
帮我总结今天的邮件
把这段 Python 代码做代码审查
每天早上 9 点提醒我喝水
查一下明天北京的天气
```

### 查看日志

```bash
openclaw logs
```

---

## 多 Agent 管理

OpenClaw 支持创建多个独立的 Agent，每个 Agent 拥有独立的工作区、会话和渠道绑定，适用于不同场景（如每日新闻推送、客服、自动化任务等）。

### 创建新 Agent

```bash
openclaw agents add <agent-name>
# 例如：
openclaw agents add dailynews
```

创建过程中的交互步骤：

| 步骤 | 说明 |
|------|------|
| **Workspace directory** | 新 Agent 的工作区路径，默认为 `~/.openclaw/workspace-<agent-name>` |
| **Configure model/auth** | 是否立即为此 Agent 配置 AI 模型和密钥（可选择稍后配置） |
| **Configure chat channels** | 选择消息渠道（Telegram、WhatsApp、Slack 等），可为新 Agent 绑定独立的 Bot |
| **Configure DM access policies** | DM 访问策略，默认为 `pairing`（配对模式，需手动授权） |
| **Route channels to agent** | 将选中的渠道绑定到此 Agent，使消息路由到对应的 Agent 处理 |

### 创建后的目录结构

```
~/.openclaw/
├── agents/
│   ├── main/              ← 默认主 Agent
│   │   └── sessions/
│   └── dailynews/         ← 新创建的 Agent
│       └── sessions/
├── workspace/             ← 主 Agent 工作区
├── workspace-dailynews/   ← 新 Agent 独立工作区
└── openclaw.json          ← 全局配置（含 Agent 和渠道绑定信息）
```

### 为新 Agent 绑定 Telegram Bot

如果为新 Agent 配置了独立的 Telegram Bot，首次在 Telegram 中向该 Bot 发消息时同样需要配对授权：

```bash
openclaw pairing approve telegram <配对码>
```

> **提示**：每个 Agent 可以绑定不同的 Telegram Bot（不同的 Bot Token），实现一个 Bot 对应一个 Agent 的独立服务。

---

## 技能扩展

技能（Skills）是 OpenClaw 的扩展模块，类似手机 App，每个技能是一个版本化的文件包，包含：

- `SKILL.md`：技能描述与使用说明
- 可选的配置文件、脚本或辅助文件
- 元数据（标签、摘要、安装要求等）

### 使用 ClawHub 安装技能

[ClawHub](https://clawhub.ai) 是 OpenClaw 官方技能市场，目前收录 3000+ 社区技能。

```bash
# 搜索技能
openclaw skills search <关键词>

# 安装技能
openclaw skills install <skill-name>

# 查看已安装技能
openclaw skills list

# 更新技能
openclaw skills update <skill-name>

# 卸载技能
openclaw skills remove <skill-name>
```

### 推荐入门技能

| 技能名 | 功能 |
|--------|------|
| `weather` | 查询天气 |
| `github` | GitHub 操作（PR、Issue 等） |
| `email` | 邮件管理 |
| `calendar` | 日历与提醒 |
| `summarize` | 文本摘要 |
| `browser` | 浏览器自动化 |

### 安装第三方技能（GitHub 仓库）

除 ClawHub 外，还可以通过 `npx skills` CLI 工具从 GitHub 仓库直接安装技能：

```bash
npx skills add <GitHub-URL>
# 例如：
npx skills add https://github.com/vercel-labs/skills/tree/main/skills/find-skills
```

安装过程中的选项说明：

| 选项 | 说明 |
|------|------|
| **Installation scope** | `Global`（全局，所有项目可用）或 `Local`（仅当前项目） |
| **Installation method** | `Symlink`（推荐，符号链接，自动同步更新）或 `Copy`（独立副本，不自动更新） |
| **Security Risk Assessments** | 三个引擎（Gen / Socket / Snyk）对技能代码的安全扫描结果 |

### 技能存储结构与加载机制

`npx skills` 是一个独立的跨代理工具技能包管理器（[skills.sh](https://skills.sh)），与 OpenClaw 本身无关。选择全局安装时，目录结构如下：

```
~/.agents/skills/<skill-name>        ← 全局技能实际文件（skills CLI 管理）
        ↑ symlink
~/.openclaw/skills/<skill-name>      ← OpenClaw 的技能目录（符号链接指向全局）
        ↑
OpenClaw 运行时从这里加载技能
```

- **`~/.agents/skills/`** — `skills` CLI 的全局存储路径，一份文件可被多个 AI 代理工具共享（OpenClaw、Cline、Cursor 等）
- **`~/.openclaw/skills/`** — OpenClaw 自身的技能目录，通过 symlink 关联到全局
- **更新一次全部生效** — 更新 `~/.agents/skills/` 下的源文件，所有链接过去的工具同步更新

> **安全提示**：第三方技能视为不可信代码，安装前请先阅读其源码。技能运行时拥有完整的代理权限，请确保信任技能来源。

---

## Hooks 自动化

Hooks 是 OpenClaw 的自动化钩子，在 Agent 执行特定命令（如 `/new`、`/reset`）时自动触发预设操作。

官方文档：[docs.openclaw.ai/automation/hooks](https://docs.openclaw.ai/automation/hooks)

### 内置 Hooks

| Hook | 说明 |
|------|------|
| `boot-md` | 启动时自动加载指定 Markdown 文件作为上下文 |
| `bootstrap-extra-files` | 启动时初始化额外的配置或资源文件 |
| `command-logger` | 记录所有 Agent 命令到日志文件，便于审计 |
| `session-memory` | 执行 `/new` 或 `/reset` 时自动将当前会话上下文保存到记忆文件 |

### 启用/禁用 Hooks

在 onboard 向导中可以多选启用，也可以在配置文件中手动管理：

```bash
# 查看已启用的 Hooks
openclaw hooks list

# 启用某个 Hook
openclaw hooks enable session-memory
```

---

## 作为后台服务运行

让 OpenClaw 在系统后台持续运行（开机自启）：

```bash
# 安装系统守护进程（macOS 使用 launchd，Linux 使用 systemd）
openclaw onboard --install-daemon

# 手动启动/停止守护进程
openclaw daemon start
openclaw daemon stop
openclaw daemon restart
```

---

## 环境变量参考

可通过环境变量自定义 OpenClaw 的路径配置：

| 变量名 | 说明 | 默认值 |
|--------|------|--------|
| `OPENCLAW_HOME` | OpenClaw 主目录 | `~/.openclaw` |
| `OPENCLAW_STATE_DIR` | 状态文件目录（覆盖默认） | — |
| `OPENCLAW_CONFIG_PATH` | 配置文件路径（覆盖默认） | — |

示例：

```bash
export OPENCLAW_HOME=/data/openclaw
openclaw start
```

---

## 常见问题

**Q: Windows 上可以直接运行吗？**
A: 推荐在 WSL2 环境下运行，原生 Windows 支持有限。

**Q: API Key 存储在哪里？是否安全？**
A: API Key 存储在本地配置文件中（默认 `~/.openclaw/config`），不会上传到任何服务器。

**Q: 支持本地模型（Ollama）吗？**
A: 支持。可通过 OpenRouter 或直接配置 Ollama 端点作为 AI Provider。

**Q: 技能默认安装在哪个目录？**
A: 默认安装在当前工作目录的 `./skills` 子目录，或回退到配置的 OpenClaw 工作区。

**Q: 如何备份数据？**
A: 直接备份 `OPENCLAW_HOME` 目录即可，包含所有配置、技能和记忆文件。

---

## 延伸阅读

### 官方资源

- [官方文档首页](https://docs.openclaw.ai)
- [快速开始指南](https://docs.openclaw.ai/start/getting-started)
- [安装文档](https://docs.openclaw.ai/install)
- [技能文档](https://docs.openclaw.ai/tools/skills)
- [GitHub 主仓库](https://github.com/openclaw/openclaw)
- [ClawHub 技能市场](https://github.com/openclaw/clawhub)

### 社区教程

- [Mastering OpenClaw: Your Ultimate Guide (Medium)](https://medium.com/@vignarajj/mastering-openclaw-your-ultimate-guide-to-setting-up-a-personal-ai-assistant-in-2026-266d8c8e66fb)
- [Use OpenClaw to Make a Personal AI Assistant (Towards Data Science)](https://towardsdatascience.com/use-openclaw-to-make-a-personal-ai-assistant/)
- [What Is OpenClaw? Complete Guide (Milvus Blog)](https://milvus.io/blog/openclaw-formerly-clawdbot-moltbot-explained-a-complete-guide-to-the-autonomous-ai-agent.md)
- [How to Run OpenClaw with DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-run-openclaw)
- [OpenClaw on NVIDIA RTX GPUs & DGX Spark](https://www.nvidia.com/en-us/geforce/news/open-claw-rtx-gpu-dgx-spark-guide/)
- [OpenClaw: How to Build a Local Always-On AI Assistant (Apify)](https://use-apify.com/blog/openclaw-personal-ai-assistant-guide)
- [Complete OpenClaw Installation Guide (EastonDev Blog)](https://eastondev.com/blog/en/posts/ai/20260205-openclaw-installation-guide/)
- [OpenClaw Setup Guide: Avoid Painful Pitfalls (Agentix Labs)](https://www.agentixlabs.com/blog/general/your-proven-openclaw-setup-guide-to-avoid-painful-pitfalls/)
- [What is OpenClaw ClawHub? (Medium)](https://medium.com/data-science-in-your-pocket/what-is-openclaw-clawhub-e123c2dd0db1)
- [OpenClaw Architecture & Setup Guide 2026 (Valletta)](https://vallettasoftware.com/blog/post/openclaw-2026-guide)

### 深度参考

- [OpenClaw DeepWiki - System Requirements](https://deepwiki.com/openclaw/openclaw/2.1-system-requirements)
- [OpenClaw DeepWiki - Getting Started with ClawHub](https://deepwiki.com/openclaw/clawhub/2-getting-started)
- [OpenClaw Setup Gist (GitHub)](https://gist.github.com/yalexx/789286610d2d59977e519108c7b8ec0a)
