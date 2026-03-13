# OpenClaw 部署指南 · OrbStack Ubuntu 24.04（macOS 本地）

> 本文记录在 macOS 上使用 OrbStack 运行 Ubuntu 24.04 虚拟机，并部署 OpenClaw 的完整步骤。AI Provider 使用 Moonshot AI（Kimi K2.5）。

---

## 环境信息

| 项目 | 值 |
|------|----|
| 宿主机 | macOS (Apple Silicon / Intel) |
| 虚拟化工具 | OrbStack |
| 操作系统 | Ubuntu 24.04 LTS |
| AI Provider | Moonshot AI (Kimi K2.5) |
| Web Search | Kimi (Moonshot) — 复用同一 API Key |
| 消息渠道 | Telegram |

---

## 第一步：安装 OrbStack

### 方法一：官网下载

访问 [orbstack.dev](https://orbstack.dev) 下载安装包，拖入 Applications 即可。

### 方法二：Homebrew 安装

```bash
brew install orbstack
```

安装后启动 OrbStack，首次启动会引导完成初始设置。

> OrbStack 免费供个人使用，比 Docker Desktop 更轻量、启动更快、更省电。

---

## 第二步：创建 Ubuntu 24.04 虚拟机

```bash
# 创建并启动 Ubuntu 24.04 虚拟机
orb create ubuntu:24.04 openclaw
```

创建完成后直接进入虚拟机：

```bash
orb shell openclaw
```

> 此时你已经在一个完整的 Ubuntu 24.04 环境中，自带 systemd，和云服务器体验一致。

---

## 第三步：安装系统依赖

Ubuntu 24.04 最小安装默认不包含 git，OpenClaw 安装时需要用到：

```bash
sudo apt update && sudo apt install -y git
```

---

## 第四步：安装 Node.js 22

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

## 第五步：安装 OpenClaw

```bash
npm install -g openclaw@latest

# 验证安装
openclaw --version
```

---

## 第六步：Onboard 初始化向导

```bash
openclaw onboard --install-daemon
```

> `--install-daemon` 会将 OpenClaw 注册为 **systemd** 服务，实现虚拟机启动后自动运行。

向导中各步骤选择如下：

| 步骤 | 选择 |
|------|------|
| AI Provider | **Moonshot AI (Kimi K2.5)** |
| API Key | 粘贴 Moonshot 平台的 Key（`sk-` 开头） |
| Web Search Provider | **Kimi (Moonshot)** — 复用同一 API Key |
| 消息渠道（Gateway） | **Telegram** |
| 启动方式（Hatch） | **Hatch in TUI** |

> Moonshot API Key 在 [platform.moonshot.cn](https://platform.moonshot.cn) → 「API Keys」页面创建。

---

## 第七步：验证服务状态

```bash
# 查看 systemd 服务状态
systemctl --user status openclaw

# 使用 openclaw 命令查看状态
openclaw status

# 查看运行日志
openclaw logs
```

---

## 第八步：Telegram 配对授权

1. 在 Telegram 中向你的 Bot 发送任意消息
2. Bot 回复配对码，格式如下：

```
OpenClaw: access not configured.

Your Telegram user id: <你的用户ID>

Pairing code: XXXXXXXX

Ask the bot owner to approve with:
openclaw pairing approve telegram XXXXXXXX
```

3. 在 macOS 上**另开一个终端窗口**，进入虚拟机执行授权命令（TUI 窗口不用关）：

```bash
orb shell openclaw
openclaw pairing approve telegram <配对码>
```

授权成功后即可在 Telegram 中正常使用 OpenClaw。

> 💡 如需再次进入 TUI 界面：`openclaw hatch tui`

---

## 常用运维命令

### OpenClaw 命令

```bash
# 进入虚拟机（从 macOS 终端执行）
orb shell openclaw

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

### OrbStack 虚拟机管理

```bash
# 从 macOS 终端管理虚拟机
orb list                  # 查看所有虚拟机
orb start openclaw        # 启动虚拟机
orb stop openclaw         # 停止虚拟机
orb restart openclaw      # 重启虚拟机
orb delete openclaw       # 删除虚拟机（慎用）
```

---

## 注意事项

**开机自启**：OrbStack 默认随 macOS 登录自动启动，虚拟机也会自动恢复运行状态。确保 OrbStack 在「系统设置 → 登录项」中保持开启。

**网络访问**：OrbStack 虚拟机直接共享 macOS 的网络，无需额外配置端口转发或网络桥接。

**合盖休眠**：MacBook 合盖后虚拟机会暂停，OpenClaw 的 Telegram 连接会断开。打开盖子后 systemd 会自动恢复服务。如需保持运行，可在「系统设置 → 电池 → 选项」中关闭自动休眠（仅接电源时建议）。

**磁盘空间**：虚拟机数据存储在 `~/.orbstack/` 目录下，确保 Mac 磁盘有足够空间。

**备份**：进入虚拟机后备份 `OPENCLAW_HOME` 目录即可，包含所有配置、技能和记忆文件。也可以从 macOS 直接访问虚拟机文件系统：

```bash
# macOS 终端中直接访问虚拟机文件
ls /Users/<你的用户名>/.orbstack/data/machines/openclaw/
```
