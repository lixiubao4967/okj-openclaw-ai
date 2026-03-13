# OpenClaw 部署指南 · AWS Ubuntu 24.04

> 本文记录在 AWS Ubuntu 24.04 LTS 上部署 OpenClaw 的完整步骤，AI Provider 使用 Moonshot AI（Kimi K2.5）。

---

## 环境信息

| 项目 | 值 |
|------|----|
| 操作系统 | Ubuntu 24.04.2 LTS (Noble Numbat) |
| AI Provider | Moonshot AI (Kimi K2.5) |
| Web Search | Kimi (Moonshot) — 复用同一 API Key |
| 消息渠道 | Telegram |

---

## 第一步：安装系统依赖

Ubuntu 24.04 最小安装默认不包含 git，OpenClaw 安装时需要用到：

```bash
sudo apt update && sudo apt install -y git
```

---

## 第二步：安装 Node.js 22

Ubuntu 24.04 自带的 Node.js 版本不满足要求，使用 nvm 安装：

```bash
# 安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# 重载环境变量
source ~/.bashrc

# 安装并启用 Node.js 22
nvm install 22
nvm use 22
nvm alias default 22

# 验证
node --version   # 期望: v22.x.x
npm --version
```

---

## 第三步：安装 OpenClaw

```bash
npm install -g openclaw@latest

# 验证安装
openclaw --version
```

---

## 第四步：Onboard 初始化向导

```bash
openclaw onboard --install-daemon
```

> `--install-daemon` 会将 OpenClaw 注册为 **systemd** 服务，实现开机自启、SSH 断开后持续运行。

向导中各步骤选择如下：

| 步骤 | 选择 |
|------|------|
| AI Provider | **Moonshot AI (Kimi K2.5)** |
| API Key | 粘贴 Moonshot 平台的 Key（`sk-` 开头） |
| Web Search Provider | **Kimi (Moonshot)** — 复用同一 API Key |
| 消息渠道（Gateway） | **Telegram** |
| 启动方式（Hatch） | **Hatch in TUI**（服务器无图形界面） |

> Moonshot API Key 在 [platform.moonshot.cn](https://platform.moonshot.cn) → 「API Keys」页面创建。

---

## 第五步：验证服务状态

```bash
# 查看 systemd 服务状态
systemctl --user status openclaw

# 使用 openclaw 命令查看状态
openclaw status

# 查看运行日志
openclaw logs
```

---

## 第六步：Telegram 配对授权

1. 在 Telegram 中向你的 Bot 发送任意消息
2. Bot 回复配对码，格式如下：

```
OpenClaw: access not configured.

Your Telegram user id: <你的用户ID>

Pairing code: XXXXXXXX

Ask the bot owner to approve with:
openclaw pairing approve telegram XXXXXXXX
```

3. 在服务器上执行授权命令：

```bash
openclaw pairing approve telegram <配对码>
```

授权成功后即可在 Telegram 中正常使用 OpenClaw。

---

## 常用运维命令

```bash
# 启动 / 停止 / 重启服务
openclaw daemon start
openclaw daemon stop
openclaw daemon restart

# 查看日志
openclaw logs

# 查看已安装技能
openclaw skills list

# 安装新技能
openclaw skills install <skill-name>
```

---

## 注意事项

**AWS 安全组**：OpenClaw 通过长轮询连接 Telegram，无需开放额外入站端口，默认安全组配置即可。

**持久化存储**（可选）：若数据盘挂载在 `/data`，建议自定义存储路径：

```bash
export OPENCLAW_HOME=/data/openclaw
openclaw start
```

也可写入 `~/.bashrc` 永久生效：

```bash
echo 'export OPENCLAW_HOME=/data/openclaw' >> ~/.bashrc
source ~/.bashrc
```

**备份**：直接备份 `OPENCLAW_HOME` 目录即可，包含所有配置、技能和记忆文件。
