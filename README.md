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
6. [基本使用](#基本使用)
7. [技能扩展（Skills & ClawHub）](#技能扩展)
8. [作为后台服务运行](#作为后台服务运行)
9. [环境变量参考](#环境变量参考)
10. [常见问题](#常见问题)
11. [延伸阅读](#延伸阅读)

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
| AI Provider API Key | — | Anthropic、OpenAI 或 OpenRouter 三选一 |
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
openclaw onboard
```

向导将引导你完成以下配置：

1. **选择 AI Provider**：Anthropic（Claude）/ OpenAI（GPT）/ OpenRouter
2. **输入 API Key**：粘贴对应平台的 API Key
3. **配置消息渠道（Gateway）**：选择 Telegram、WhatsApp、Slack 等
4. **设置工作区路径（Workspace）**：本地存储技能和记忆文件的目录
5. **安装初始技能**：可选择安装推荐的基础技能

> 建议至少配置一个**备用模型（Fallback Model）**，防止主模型达到速率限制时中断服务。

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

> **安全提示**：第三方技能视为不可信代码，安装前请先阅读其源码。

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
