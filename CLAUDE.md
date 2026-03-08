# OpenClaw AI 项目说明

本仓库用于记录 OpenClaw 的安装、配置与使用经验，供团队参考。

## 项目结构

- `README.md` - OpenClaw 完整安装与使用指南（中文）

## 关键约定

### 初始化命令

始终使用 `--install-daemon` 参数进行初始化，以启用系统守护进程（开机自启）：

```bash
openclaw onboard --install-daemon
```

### AI Provider

- **Anthropic（Claude）**：公司团队账号，API Key 从 `console.anthropic.com` 获取
- **其他平台**（OpenAI、OpenRouter 等）：使用免费账号

### 平台账号说明

- `claude.ai` 账号（`xiubao.li@okcoin.jp`，OKJ Teams）**不等于** Anthropic API Key
- API Key 需在 `console.anthropic.com` 单独创建，格式为 `sk-ant-` 开头
