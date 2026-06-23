# 🦞 OpenClaw 使用教程

> 版本 2026.5.27 | 文档日期 2026-05-30

---

## 目录

1. [什么是 OpenClaw](#1-什么是-openclaw)
2. [安装与初始化](#2-安装与初始化)
3. [网关 (Gateway)](#3-网关-gateway)
4. [控制面板 (Control UI)](#4-控制面板-control-ui)
5. [配置文件详解](#5-配置文件详解)
6. [频道 (Channels)](#6-频道-channels)
7. [技能 (Skills)](#7-技能-skills)
8. [工具 (Tools)](#8-工具-tools)
9. [节点 (Nodes)](#9-节点-nodes)
10. [多智能体路由](#10-多智能体路由)
11. [安全意识与最佳实践](#11-安全意识与最佳实践)
12. [常见操作速查](#12-常见操作速查)

---

## 1. 什么是 OpenClaw

OpenClaw 是一个**自托管网关**，连接聊天应用（Discord、Telegram、WhatsApp、Signal、Slack、iMessage 等）到 AI 智能体。你只需运行一个网关进程，就能从手机、电脑、Web 等各种渠道与 AI 助手对话。

### 核心特性

| 特性 | 说明 |
|------|------|
| 🏠 **自托管** | 跑在自己的机器上，数据不经过第三方 |
| 🔗 **多频道** | 一个网关同时服务 Discord、Telegram、WhatsApp、WebChat 等 |
| 🧠 **智能体原生** | 支持工具调用、会话、记忆、多智能体路由 |
| 🌐 **Web Control UI** | 浏览器管理面板，聊天、配置、节点管理一站式 |
| 📱 **移动节点** | iOS/Android 设备配对，支持摄像头、画布、语音 |
| 🔌 **插件系统** | 插件扩展频道、技能和工具 |
| 📦 **技能系统** | AgentSkills 兼容的模块化技能包 |
| 🧩 **ClawHub** | 公开技能注册中心，一键安装社区技能 |

### 架构概览

```
聊天应用 + 插件 ──▶  Gateway  ──▶  AI 智能体 (Pi)
                      │
                      ├── Web Control UI
                      ├── CLI
                      ├── macOS 应用
                      └── iOS/Android 节点
```

**网关 (Gateway)** 是唯一真相源：负责所有会话、路由和频道连接。

---

## 2. 安装与初始化

### 2.1 系统要求

- **Node.js**：Node 24 推荐（Node 22.19+ 也支持）
- **操作系统**：macOS、Linux、Windows（WSL2 推荐）、树莓派

### 2.2 安装方式

#### 方式一：一键脚本（推荐）

```bash
# macOS / Linux
curl -fsSL https://openclaw.ai/install.sh | bash

# Windows PowerShell
iwr -useb https://openclaw.ai/install.ps1 | iex
```

#### 方式二：npm 全局安装

```bash
npm install -g openclaw@latest
```

#### 方式三：Docker / Nix

参见 [https://docs.openclaw.ai/install](https://docs.openclaw.ai/install)

### 2.3 初始化配置

```bash
# 运行引导向导，自动安装 systemd 服务
openclaw onboard --install-daemon
```

引导过程会询问：
- 选择模型提供商（Anthropic、OpenAI、Google 等）
- 配置 API Key
- 设置网关安全选项

### 2.4 验证安装

```bash
# 检查网关状态
openclaw gateway status

# 打开 Web 控制面板
openclaw dashboard
```

---

## 3. 网关 (Gateway)

网关是 OpenClaw 的核心进程，管理所有通信和会话。

### 3.1 启动与停止

```bash
# 前台运行（调试用）
openclaw gateway

# 后台服务（systemd）
systemctl --user start openclaw-gateway.service
systemctl --user stop openclaw-gateway.service
systemctl --user restart openclaw-gateway.service
systemctl --user status openclaw-gateway.service
```

### 3.2 网关状态检查

```bash
openclaw gateway status
```

输出内容包括：
- 运行状态（running/stopped）
- 绑定地址和端口
- Dashboard 地址
- 认证模式
- 连通性检测

### 3.3 网络绑定模式

网关支持多种网络绑定模式：

| 模式 | 说明 | 配置值 |
|------|------|--------|
| **loopback** | 仅本地访问（默认） | `"loopback"` |
| **lan** | 局域网可访问 | `"lan"` |
| **tailnet** | Tailscale 网络 | `"tailnet"` |
| **auto** | 自动选择 | `"auto"` |
| **custom** | 自定义绑定 | `"custom"` |

配置方式：

```json5
{
  "gateway": {
    "bind": "lan",           // 局域网模式
    "port": 18789,           // 端口
    "auth": {
      "mode": "token",       // 令牌认证
      "token": "your-token-here"
    }
  }
}
```

### 3.4 认证模式

| 模式 | 说明 | 适用场景 |
|------|------|----------|
| `token` | 共享令牌认证 | 局域网/远程访问 |
| `none` | 无认证 | 仅本地信任环境 |
| `trusted-proxy` | 信任代理头 | 反向代理后 |

### 3.5 Control UI 安全设置

当通过 HTTP 远程访问控制面板时，浏览器会因安全问题报错。解决方案：

**方案一：启用不安全认证（局域网内可接受）**

```json5
{
  "gateway": {
    "controlUi": {
      "allowInsecureAuth": true
    }
  }
}
```

**方案二：使用 Tailscale Serve（推荐）**

```bash
# 启用 Tailscale
openclaw tailscale serve
```

**方案三：SSH 隧道**

```bash
ssh -N -L 18789:127.0.0.1:18789 user@gateway-host
```

### 3.6 日志查看

```bash
# 网关日志
journalctl --user -u openclaw-gateway.service -n 200 --no-pager

# 文件日志
tail -f /tmp/openclaw/openclaw-2026-05-30.log

# 重启记录
cat ~/.openclaw/logs/gateway-restart.log
```

### 3.7 健康检查

通过 WebSocket 协议：

```json
// 请求
{ "type": "req", "id": "1", "method": "health", "params": {} }
// 响应
{ "type": "res", "id": "1", "ok": true, "payload": { ... } }
```

---

## 4. 控制面板 (Control UI)

Control UI 是 Web 版的管理界面。

### 4.1 打开方式

```bash
# 默认浏览器打开
openclaw dashboard

# 手动访问（局域网）
http://192.168.1.x:18789/

# 本地访问
http://127.0.0.1:18789/
```

### 4.2 主要功能

- **💬 聊天**：直接与 AI 智能体对话
- **⚙️ 设置**：网关配置、模型设置
- **📋 会话管理**：查看和管理所有会话
- **📦 技能管理**：启用/禁用技能
- **📡 节点管理**：查看已配对的设备节点
- **📊 系统状态**：资源使用、连接状态

### 4.3 WebSocket 协议基础

Control UI 和所有客户端通过 WebSocket 与网关通信：

```
连接流程:
1. Client ──▶ Gateway: req:connect { auth: { token: "..." } }
2. Gateway ──▶ Client: res:ok { hello-ok }
3. Client ──▶ Gateway: req:agent { message: "你好" }
4. Gateway ──▶ Client: event:agent (流式响应)
5. Gateway ──▶ Client: res:agent (最终结果)
```

---

## 5. 配置文件详解

配置文件位于 `~/.openclaw/openclaw.json`，使用 JSON5 格式（支持注释和尾部逗号）。

### 5.1 基本结构

```json5
{
  // ===== 网关设置 =====
  "gateway": {
    "port": 18789,
    "bind": "loopback",      // loopback | lan | tailnet | auto | custom
    "auth": {
      "mode": "token",       // token | none | trusted-proxy
      "token": "你的令牌"
    },
    "controlUi": {
      "allowInsecureAuth": true
    }
  },

  // ===== 智能体默认配置 =====
  "agents": {
    "defaults": {
      "workspace": "/home/lk/.openclaw/workspace",
      "model": {
        "primary": "deepseek/deepseek-v4-flash"   // provider/model
      },
      "models": {
        "provider/model": { "alias": "昵称" }
      },
      "skills": ["github", "weather"],            // 技能白名单
      "heartbeat": {
        "prompt": "检查是否有新消息..."
      }
    }
  },

  // ===== 工具配置 =====
  "tools": {
    "profile": "coding"      // coding | full
  },

  // ===== 技能设置 =====
  "skills": {
    "entries": {
      "image-lab": {
        "enabled": true,
        "env": { "GEMINI_API_KEY": "你的密钥" }
      },
      "browser-automation": { "enabled": true }
    },
    "load": {
      "extraDirs": ["~/Projects/skills"],
      "watch": true
    }
  },

  // ===== 频道设置 =====
  "channels": {
    "whatsapp": {
      "allowFrom": ["+8613900000000"]
    }
  },

  // ===== 会话设置 =====
  "session": {
    "dmScope": "per-channel-peer"
  }
}
```

### 5.2 模型配置

```json5
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "openai/gpt-4o",          // 主模型
        "reasoning": "openai/o3",             // 推理模型（可选）
        "imageGeneration": "google/gemini-3-pro-image-preview"  // 图像生成
      }
    }
  },
  "models": {
    "providers": {
      "openai": {
        "baseUrl": "https://api.openai.com/v1",
        "apiKey": { "env": "OPENAI_API_KEY" },
        "models": [
          { "id": "gpt-4o", "cost": { "input": 2.5, "output": 10 } }
        ]
      }
    }
  }
}
```

### 5.3 工作空间文件

工作空间目录（默认 `~/.openclaw/workspace`）包含以下关键文件：

| 文件             | 用途               |
| -------------- | ---------------- |
| `AGENTS.md`    | 操作指令 + "记忆"      |
| `SOUL.md`      | 人格设定、边界、语气       |
| `IDENTITY.md`  | 智能体的名称、画风、头像     |
| `USER.md`      | 用户信息             |
| `TOOLS.md`     | 个性化工具笔记          |
| `BOOTSTRAP.md` | 首次运行初始化（完成后自动删除） |
| `memory/`      | 每日记忆文件           |
| `MEMORY.md`    | 长期记忆             |
| `skills/`      | 工作空间级技能          |

---

## 6. 频道 (Channels)

OpenClaw 支持多种聊天平台作为输入/输出频道。

### 6.1 支持的频道

| 频道 | 状态 | 配置方式 |
|------|------|----------|
| **Control UI** (WebChat) | 内置 | 开箱即用 |
| **Discord** | 内置 | Bot Token |
| **Telegram** | 内置 | Bot Token |
| **WhatsApp** | 内置 | 二维码扫描 |
| **Signal** | 内置 | Signal CLI |
| **Slack** | 内置 | Bot Token |
| **iMessage** | 内置 | macOS 节点 |
| **Matrix** | 插件 | 配置 Homeserver |
| **Google Chat** | 内置 | 服务账号 |
| **Zalo** | 插件 | API Key |
| **Feishu/飞书** | 内置 | App 凭证 |
| **LINE** | 内置 | Channel Token |
| **IRC** | 插件 | 服务器地址 |
| **Microsoft Teams** | 内置 | 应用注册 |

### 6.2 频道配置示例

```json5
{
  "channels": {
    "telegram": {
      "token": "YOUR_BOT_TOKEN"
    },
    "whatsapp": {
      "allowFrom": ["+8613900000000"],
      "groups": {
        "*": { "requireMention": true }
      }
    },
    "discord": {
      "token": "YOUR_DISCORD_BOT_TOKEN"
    }
  }
}
```

### 6.3 群聊模式

```json5
{
  "messages": {
    "groupChat": {
      "mentionPatterns": ["@openclaw", "@bot"]
    }
  }
}
```

设置后只在这些提及模式匹配时回复，避免群聊中无故抢话。

---

## 7. 技能 (Skills)

技能是 OpenClaw 的模块化功能包，告诉智能体如何使用特定工具。

### 7.1 技能加载位置（优先级从高到低）

| 优先级 | 位置 | 路径 |
|--------|------|------|
| 1 | 工作空间技能 | `<workspace>/skills` |
| 2 | 项目智能体技能 | `<workspace>/.agents/skills` |
| 3 | 个人智能体技能 | `~/.agents/skills` |
| 4 | 托管技能 | `~/.openclaw/skills` |
| 5 | 内置技能 | 随安装包一起 |
| 6 | 额外目录 | `skills.load.extraDirs` |

同名技能，优先级高的覆盖低的。

### 7.2 SKILL.md 格式

每个技能是一个文件夹，内含 `SKILL.md`：

```markdown
---
name: image-lab
description: 通过 AI 生成或编辑图片
metadata: {"openclaw": { "emoji": "🎨", "requires": { "bins": ["uv"] } }}
---

# Image Lab 技能

使用此技能来进行图像生成和编辑操作。

{baseDir} 指向技能文件夹路径。
```

### 7.3 从 ClawHub 安装技能

```bash
# 安装到当前工作空间
openclaw skills install <skill-slug>

# 安装到全局共享
openclaw skills install <skill-slug> --global

# 从 Git 安装
openclaw skills install git:owner/repo@ref

# 从本地路径安装
openclaw skills install ./path/to/skill --as my-tool

# 更新所有技能
openclaw skills update --all
```

浏览技能：https://clawhub.ai

### 7.4 技能配置

```json5
{
  "skills": {
    "entries": {
      "image-lab": {
        "enabled": true,
        "apiKey": { "source": "env", "provider": "default", "id": "GEMINI_API_KEY" },
        "env": { "GEMINI_API_KEY": "你的密钥" }
      },
      "peekaboo": { "enabled": true },
      "sag": { "enabled": false }     // 禁用
    },
    "allowBundled": ["gemini", "peekaboo"]  // 只允许这些内置技能
  }
}
```

### 7.5 技能白名单（按智能体）

```json5
{
  "agents": {
    "defaults": {
      "skills": ["github", "weather"]      // 默认智能体可用
    },
    "list": [
      { "id": "writer" },                   // 继承 defaults
      { "id": "docs", "skills": ["docs-search"] },  // 替换
      { "id": "locked-down", "skills": [] }  // 无技能
    ]
  }
}
```

### 7.6 创建自定义技能

创建一个技能文件夹，内含 `SKILL.md`：

```bash
mkdir -p ~/.openclaw/workspace/skills/my-tool
```

编辑 `SKILL.md`：

```markdown
---
name: my-tool
description: 我自定义的工具技能
---

# 我的工具

在这里描述如何使用你的工具。
```

---

## 8. 工具 (Tools)

OpenClaw 提供丰富的内置工具供智能体调用。

### 8.1 核心工具

| 工具 | 用途 |
|------|------|
| `read` | 读取文件内容 |
| `write` | 写入/创建文件 |
| `edit` | 精确编辑文件 |
| `exec` | 执行 Shell 命令 |
| `web_search` | 网络搜索 |
| `web_fetch` | 抓取网页内容 |
| `memory_get` / `memory_search` | 记忆读取和搜索 |
| `browser` | 浏览器自动化控制 |
| `image_generation` | AI 图像生成 |
| `tts` | 文字转语音 |
| `subagents` | 子智能体管理 |
| `sessions_list` / `sessions_history` | 会话管理 |

### 8.2 工具策略配置

```json5
{
  "tools": {
    "profile": "coding",       // coding | full
    "exec": {
      "approval": "on-miss",   // off | on-miss | always
      "applyPatch": true       // 启用 apply_patch 工具
    }
  }
}
```

### 8.3 浏览器工具

需要额外安装 Chromium：

```bash
# Linux 安装 Chromium
sudo apt install chromium
```

启用浏览器技能：

```json5
{
  "skills": {
    "entries": {
      "browser-automation": { "enabled": true }
    }
  }
}
```

### 8.4 文件执行安全

| 执行模式 | 说明 |
|----------|------|
| `host=auto` | 自动选择主机 |
| `host=sandbox` | 沙盒中执行（需要 Docker） |
| `elevated=true` | 提权执行（需要审批） |
| `approval=on-miss` | 仅对新命令需要审批 |

---

## 9. 节点 (Nodes)

节点是配对的移动/桌面设备，提供额外能力。

### 9.1 支持的节点类型

- **iOS**：iPhone/iPad，通过 OpenClaw App 配对
- **Android**：通过 OpenClaw App 配对
- **macOS**：通过 macOS 应用配对
- **Headless**：无界面设备

### 9.2 节点能力

| 能力 | iOS | Android | macOS |
|------|-----|---------|-------|
| 💬 聊天 | ✅ | ✅ | ✅ |
| 📷 相机 | ✅ | ✅ | ❌ |
| 🖼️ Canvas | ✅ | ✅ | ✅ |
| 🎤 语音 | ✅ | ✅ | ✅ |
| 📍 定位 | ✅ | ✅ | ✅ |
| 📺 屏幕录制 | ❌ | ❌ | ✅ |
| 📱 联系人 | ✅ | ✅ | ✅ |
| ⏰ 提醒 | ✅ | ✅ | ✅ |

### 9.3 配对流程

1. 在移动设备上安装 OpenClaw App
2. 连接到网关（需网关地址 + 令牌）
3. 网关端确认配对
4. 验证设备身份

### 9.4 节点配置

```json5
{
  "gateway": {
    "nodes": {
      "denyCommands": [           // 禁用的节点命令
        "camera.snap",
        "camera.clip",
        "screen.record",
        "contacts.add",
        "sms.send"
      ]
    }
  }
}
```

---

## 10. 多智能体路由

支持在不同工作空间或频道路由到不同智能体。

### 10.1 配置多智能体

```json5
{
  "agents": {
    "defaults": {
      "workspace": "/home/lk/.openclaw/workspace",
      "model": { "primary": "openai/gpt-4o" }
    },
    "list": [
      {
        "id": "writer",
        "workspace": "/home/lk/.openclaw/workspaces/writer",
        "model": { "primary": "anthropic/claude-sonnet-4" },
        "skills": ["writing"]
      },
      {
        "id": "coder",
        "workspace": "/home/lk/.openclaw/workspaces/coder",
        "skills": ["github", "code-review"]
      }
    ]
  },
  "multiAgent": {
    "routing": [
      { "from": "telegram:chat:123", "to": "writer" },
      { "from": "discord:channel:456", "to": "coder" }
    ]
  }
}
```

### 10.2 会话管理

每个智能体拥有独立的工作空间、会话存储和技能配置。会话存储在：

```
~/.openclaw/agents/<agentId>/sessions/<SessionId>.jsonl
```

### 10.3 队列与转向

- `/queue steer`：消息转向当前活动运行（默认）
- `/queue followup`：排队等待下一轮
- `/queue interrupt`：中断当前运行
- `/queue collect`：收集消息等待后续处理

---

## 11. 安全意识与最佳实践

### 11.1 网络安全

| 场景       | 推荐做法                  |
| -------- | --------------------- |
| 仅本地使用    | `bind: loopback`      |
| 局域网使用    | `bind: lan` + 强令牌     |
| 公网访问     | Tailscale / VPN，不建议直连 |
| 需要 HTTPS | Tailscale Serve 或反向代理 |
|          |                       |

### 11.2 令牌安全

- 令牌保存在 `openclaw.json` 中，不要分享给不信任的人
- 定期更换令牌
- 不同环境使用不同令牌

### 11.3 技能安全

```md
⚠️ 第三方技能视为不受信任代码，使用前请阅读。
推荐在沙盒中运行不信任的输入。
```

### 11.4 最小权限原则

```json5
{
  "agents": {
    "list": [
      {
        "id": "locked-down",
        "skills": [],                  // 无额外技能
        "sandbox": { "enabled": true } // 强制沙盒
      }
    ]
  }
}
```

### 11.5 避免暴露的安全风险

- 不要将网关直接暴露在公网
- 使用 `allowFrom` 限制频道来源
- 群聊中设置 `requireMention: true`
- 敏感命令设置审批流程

---

## 12. 常见操作速查

### 12.1 CLI 命令速查

```bash
# 安装
npm install -g openclaw@latest
openclaw onboard --install-daemon

# 网关管理
openclaw gateway                         # 前台启动
openclaw gateway status                  # 查看状态
openclaw gateway logs                    # 查看日志
openclaw dashboard                       # 打开控制面板
openclaw doctor                          # 诊断修复

# 技能管理
openclaw skills install <slug>           # 安装技能
openclaw skills update --all             # 更新所有
openclaw skills list                     # 列出已安装

# 配置
openclaw config                          # 编辑配置
openclaw setup                           # 重新设置
openclaw migrate codex                   # 从 Codex 迁移

# 其他
openclaw tailscale serve                 # 启用 Tailscale
openclaw version                         # 查看版本
```

### 12.2 systemd 服务管理

```bash
systemctl --user start openclaw-gateway
systemctl --user stop openclaw-gateway
systemctl --user restart openclaw-gateway
systemctl --user enable openclaw-gateway
systemctl --user status openclaw-gateway
journalctl --user -u openclaw-gateway -f
```

### 12.3 文件路径速查

| 内容 | 路径 |
|------|------|
| 主配置 | `~/.openclaw/openclaw.json` |
| 工作空间 | `~/.openclaw/workspace/` |
| 工作空间技能 | `~/.openclaw/workspace/skills/` |
| 全局技能 | `~/.openclaw/skills/` |
| 会话数据 | `~/.openclaw/agents/main/sessions/` |
| 网关日志 | `/tmp/openclaw/openclaw-*.log` |
| 认证配置 | `~/.openclaw/agents/main/agent/auth-profiles.json` |
| 重启记录 | `~/.openclaw/logs/gateway-restart.log` |

### 12.4 问题排查

| 问题         | 解决                                                      |
| ---------- | ------------------------------------------------------- |
| 网关无法启动     | 运行 `openclaw doctor`                                    |
| 配置文件错误     | 检查 `openclaw.json` 语法                                   |
| 控制面板不能远程访问 | 设置 `bind: "lan"` + `allowInsecureAuth: true`，或使用 SSH 隧道 |
| 令牌无效       | 检查 `openclaw.json` 中的 token 值                           |
| 技能没有加载     | 检查技能白名单和 gating 条件（需要特定二进制或环境变量）                        |
| 频道连接失败     | 确认频道凭证正确，检查日志                                           |
| 浏览器工具报错    | 确认 Chromium 已安装，参考浏览器排障文档                               |
| 记忆搜索不可用    | 检查 embedding provider API Key 配置                        |

---

> **更多资源**
>
> - 官方文档：https://docs.openclaw.ai
> - 技能市场：https://clawhub.ai
> - GitHub：https://github.com/openclaw/openclaw
> - 技能创建指南：https://docs.openclaw.ai/tools/creating-skills
