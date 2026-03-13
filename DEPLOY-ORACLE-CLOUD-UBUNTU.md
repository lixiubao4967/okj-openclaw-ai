# OpenClaw 部署指南 · Oracle Cloud 免费层 Ubuntu 24.04

> 本文记录在 Oracle Cloud Infrastructure (OCI) 免费层 ARM 实例上部署 OpenClaw 的完整步骤，AI Provider 使用 Moonshot AI（Kimi K2.5）。

---

## 环境信息

| 项目 | 值 |
|------|----|
| 云平台 | Oracle Cloud Infrastructure (OCI) Always Free |
| 实例类型 | VM.Standard.A1.Flex（ARM） |
| 操作系统 | Ubuntu 24.04 LTS (aarch64) |
| AI Provider | Moonshot AI (Kimi K2.5) |
| Web Search | Kimi (Moonshot) — 复用同一 API Key |
| 消息渠道 | Telegram |

---

## 第一步：注册 Oracle Cloud 账号

1. 访问 [cloud.oracle.com](https://cloud.oracle.com) 点击 **Sign Up**
2. 填写注册信息：
   - 邮箱地址
   - 手机号验证
   - **信用卡**（仅验证身份，免费层不扣费，会有约 $1 预授权，几天后自动退回）
3. 选择 **Home Region**（主区域，注册后不可更改）

### 区域选择建议

| 区域 | 代码 | 适合场景 |
|------|------|---------|
| **Tokyo** | ap-tokyo-1 | 日本访问延迟最低，推荐 |
| **Seoul** | ap-seoul-1 | 东亚备选 |
| **Singapore** | ap-singapore-1 | 东南亚备选 |

> ⚠️ Home Region 一旦选定无法更改，请谨慎选择。

---

## 第二步：创建免费 ARM 实例

登录 OCI Console 后：

1. 左上角菜单 → **Compute** → **Instances** → **Create Instance**
2. 按以下配置创建：

| 配置项 | 选择 |
|--------|------|
| Name | `openclaw`（随意） |
| Compartment | 默认 root compartment |
| Image | **Canonical Ubuntu 24.04**（点击 Change image 选择） |
| Shape | **VM.Standard.A1.Flex**（Ampere ARM，免费） |
| OCPU | **1**（免费层总共 4 核，1 核即够用） |
| Memory | **6 GB**（免费层总共 24GB，6GB 即够用） |
| Boot volume | 默认 47 GB（免费层最多 200GB） |
| Networking | 使用默认 VCN，或新建一个 |
| SSH Key | 上传公钥，或点击 Generate 生成（**务必保存好私钥**） |

> 💡 免费层 ARM 额度：总计 4 OCPU + 24 GB 内存，可分配给 1~4 个实例。

### 抢不到实例？

热门区域（Tokyo 等）的免费 ARM 实例经常缺货，创建时可能报错：

```
Out of host capacity.
```

解决办法：
- 换个时间段重试（凌晨或工作日白天成功率更高）
- 先用较小配置（1 OCPU + 6GB），比 4 OCPU + 24GB 更容易抢到
- 换到较冷门的区域

---

## 第三步：配置网络安全列表

OpenClaw 通过长轮询连接 Telegram，**不需要开放额外入站端口**。

默认安全列表已包含 SSH（22 端口）入站规则，无需额外配置。

如需确认：OCI Console → **Networking** → **Virtual Cloud Networks** → 选择 VCN → **Security Lists** → 检查是否有：

| 方向 | 协议 | 端口 | 来源 |
|------|------|------|------|
| Ingress | TCP | 22 | 0.0.0.0/0 |

---

## 第四步：SSH 连接实例

实例创建完成后，在 Instance Details 页面找到 **Public IP Address**，然后连接：

```bash
ssh -i <私钥路径> ubuntu@<实例公网IP>
```

例如：

```bash
ssh -i ~/.ssh/oci_key ubuntu@129.xxx.xxx.xxx
```

> OCI Ubuntu 实例默认用户名为 `ubuntu`。

---

## 第五步：安装 Node.js 22

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

> ARM 架构完全支持 Node.js 22，无需特殊处理。

---

## 第六步：安装 OpenClaw

```bash
npm install -g openclaw@latest

# 验证安装
openclaw --version
```

---

## 第七步：Onboard 初始化向导

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

## 第八步：验证服务状态

```bash
# 查看 systemd 服务状态
systemctl --user status openclaw

# 使用 openclaw 命令查看状态
openclaw status

# 查看运行日志
openclaw logs
```

---

## 第九步：Telegram 配对授权

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

**免费层限制**：
- ARM 实例：总计 4 OCPU + 24 GB 内存（永久免费）
- Boot volume：总计 200 GB（永久免费）
- 出站流量：每月 10 TB（足够使用）
- **不要升级为付费账号**，除非你确定需要，保持 Always Free 即可

**OCI 防火墙（iptables）**：OCI Ubuntu 镜像默认启用了 iptables 规则，如果后续需要开放端口（如 Web 界面），除了安全列表外还需配置 iptables：

```bash
sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport <端口号> -j ACCEPT
sudo netfilter-persistent save
```

**持久化存储**（可选）：若挂载了额外 Block Volume，建议自定义存储路径：

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
