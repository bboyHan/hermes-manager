# Hermes Agent Web Dashboard 部署操作手册

## 概述

本手册详细说明如何部署和使用 Hermes Agent 的 Web Dashboard。Hermes Agent 内置了 Web Dashboard，无需安装额外的前端框架，一条命令即可启动。

---

## 前置条件

1. **Hermes Agent 已安装**
   - 安装命令：`curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash`
   - 验证已安装：`hermes --version`

2. **系统要求**
   - POSIX 系统 (Linux/macOS)
   - 已安装 Node.js 18+（首次启动时需要构建 Web UI）

---

## 一键部署

### 启动 Dashboard

```bash
hermes dashboard --port 9119 --no-open
```

**参数说明：**

| 参数 | 说明 | 默认值 |
|------|------|-----------|
| `--port <port>` | Dashboard 监听端口 | 9119 |
| `--host <host>` | 监听地址 | 127.0.0.1 |
| `--no-open` | 启动后不自动打开浏览器（后台模式使用） | false |
| `--insecure` | 允许绑定到非 localhost（⚠️ 安全警告） | 不使用 |
| `--tui` | 启用浏览器内嵌 Chat 模式（通过 PTY/WebSocket） | 不使用 |
| `--skip-build` | 跳过 Web UI 构建步骤，直接服务已有 dist | 不使用 |
| `--stop` | 停止所有正在运行的 Dashboard 进程 | 不使用 |
| `--status` | 列出正在运行的 Dashboard 进程 | 不使用 |

### 启动后验证

```bash
# 检查进程状态
hermes dashboard --status

# 访问 Dashboard
# 浏览器打开: http://localhost:9119
```

---

## Dashboard 功能模块说明

Dashboard 提供了以下管理模块：

### 1. SESSIONS - 会话管理
- 查看所有对话会话
- 恢复（resume）指定会话
- 删除会话
- 重命名会话
- 会话统计信息（总量/活跃/归档）

### 2. MODELS - 模型管理
- 查看当前模型配置
- 切换不同 Provider
- 查看候选模型列表
- 修改默认模型

### 3. LOGS - 日志系统
- 查看 Hermes 运行日志
- 查看错误日志
- 日志级别调整

### 4. CRON - 定时任务管理
- 创建定时任务
- 查看已计划的 Cron 任务
- 暂停/恢复/移除任务
- 触发/编辑任务执行

### 5. SKILLS - 技能管理
- 浏览已安装技能
- 从 Hub 安装/卸载技能
- 搜索技能
- 更新技能库

### 6. PLUGINS - 插件管理
- 列出已安装插件
- 安装/卸载插件

### 7. MCP - MCP 服务器管理
- 添加 MCP 服务器（--url 或 --command）
- 测试 MCP 连接
- 配置工具选择
- 管理 MCP 列表

### 8. CHANNELS - 频道管理
- 查看已连接的消息平台
- 管理订阅渠道
- 设置 Home Channel

### 9. WEBHOOKS - Webhook 管理
- 创建 webhook 订阅
- 列出所有 webhook
- 测试 webhook
- 移除 webhook

### 10. PAIRING - 设备配对管理
- 查看已配对设备
- 审批配对请求
- 撤销配对

### 11. PROFILES - 方案管理
- 列出所有方案
- 创建/删除/切换方案
- 克隆方案
- 导出/导入方案

### 12. CONFIG - 配置管理
- 查看所有配置项
- 使用 `hermes config set KEY VAL` 修改配置
- 打开配置文件编辑器
- 检查配置有效性

### 13. KEYS - API 密钥管理
- 查看所有 Provider 的 API 密钥
- 添加/更新密钥
- 使用 `hermes auth add <provider>` 交互添加

### 14. SYSTEM - 系统状态
- 运行 `hermes doctor` 检查系统完整性
- 检查依赖项
- 检查配置错误
- 系统信息概览

### 15. DOCUMENTATION - 文档
- 在线查看 Hermes Agent 官方文档
- 配置参考
- API 参考
- 开发者指南

---

## 常用操作

### 启动 Dashboard（前台模式）

```bash
# 端口 9119 默认
hermes dashboard

# 自定义端口
hermes dashboard --port 8080

# 启用内嵌 Chat 界面
hermes dashboard --tui
```

**前台模式**会在当前终端运行，按 `Ctrl+C` 停止。浏览器会自动打开。

### 启动 Dashboard（后台模式）

```bash
# 后台启动，不自动打开浏览器
hermes dashboard --port 9119 --no-open &

# 查看运行状态
hermes dashboard --status

# 停止 Dashboard
hermes dashboard --stop
```

### 切换到特定方案

```bash
# 启动时使用默认方案
hermes dashboard

# 启动时使用指定方案
hermes -p my-profile dashboard

# 切换默认方案
hermes profile use my-profile
```

### 启用内嵌 Chat（浏览器内直接对话）

```bash
# 方式 1：启动时启用
hermes dashboard --tui

# 方式 2：环境变量
export HERMES_DASHBOARD_TUI=1
hermes dashboard
```

启用后，Dashboard 会提供一个聊天窗口，支持流式输入输出。

---

## 安全注意事项

### ⚠️ 关于 `--insecure` 参数

**强烈不建议**使用 `--insecure` 参数暴露到外部网络，原因如下：

1. **API 密钥泄露风险** - Dashboard 会传输 API 密钥
2. **未授权访问** - 任何人都可以通过 HTTP 操作你的 Agent
3. **会话劫持** - 可以读取/操控所有对话会话

**正确做法：**
- 使用反向代理（如 Nginx）设置 HTTPS
- 配置 HTTP Basic Auth
- 限制源 IP 白名单
- 始终使用 `127.0.0.1` 或内网 IP

---

## 常见问题排查

### 问题 1："Error: could not connect to the dashboard"

**原因：** Dashboard 未启动或端口已被占用

**解决方案：**
```bash
# 检查是否正在运行
hermes dashboard --status

# 如果有残留进程，先停止
hermes dashboard --stop

# 检查端口占用
lsof -i :9119

# 使用其他端口启动
hermes dashboard --port 9120
```

### 问题 2："Node.js not found" 或 Web UI 构建失败

**原因：** 缺少 Node.js 或 npm 环境

**解决方案：**
```bash
# 安装 Node.js (Ubuntu/Debian)
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# 验证
node --version
npm --version

# 重新启动 Dashboard
hermes dashboard
```

**或者**使用 `--skip-build` 参数跳过构建（如果已有 dist）：
```bash
hermes dashboard --skip-build
```

### 问题 3：Dashboard 启动后无法通过局域网访问

**原因：** 默认只监听 `127.0.0.1`（本地回环地址）

**解决方案：**
```bash
# 方式 1：绑定到局域网 IP（仅限受信任网络！）
hermes dashboard --host 0.0.0.0

# ⚠️ 注意：这会暴露 Dashboard 到整个网络！
# 推荐配合 nginx 反向代理 + HTTPS 使用
```

### 问题 4：Dashboard 中所有模块空无一物

**原因：** Gateway 未运行或配置有误

**解决方案：**
```bash
# 检查 Gateway 状态
hermes gateway status

# 启动 Gateway
hermes gateway start

# 检查配置文件
hermes config check

# 查看日志
hermes logs
```

### 问题 5：WebSocket 连接异常

**症状：** "Failed to connect - Bad WebSocket Handshake"

**可能原因：** 防火墙拦截 WebSocket 连接或代理配置问题

**解决方案：**
1. 确保防火墙允许 WebSocket 端口
2. 如果使用反向代理（如 Nginx），配置 WebSocket 头：
```nginx
map $http_upgrade $connection_upgrade {
    default upgrade;
    '' close;
}

server {
    location / {
        proxy_pass http://localhost:9119;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
    }
}
```

---

## 与现有平台集成

Web Dashboard 不替代消息平台 gateway，你可以同时运行：

```bash
# Gateway 运行消息平台（飞书/Telegram/Discord 等）
hermes gateway run

# Dashboard 运行 Web 管理界面（独立进程）
hermes dashboard
```

两个进程协同工作，互不影响。

---

## 更新与维护

### 更新 Dashboard

```bash
# 更新 Hermes Agent（包含 Dashboard）
hermes update

# Dashboard 会自动更新
```

### 定期清理

Dashboard 不会自动清理旧会话：

```bash
# 通过 Dashboard 界面操作
# 或通过 CLI
hermes sessions prune --older-than 30d  # 清理 30 天前的会话
```

---

## 访问地址总结

| 场景 | 地址 |
|------|------|
| 本地开发 | `http://localhost:9119` |
| 局域网访问（推荐 Nginx） | `https://your-server/hermes` |
| 自定义端口 | `http://localhost:自定义端口` |

---

## 参考命令速查表

```bash
# 基础操作
hermes dashboard                              # 启动 Dashboard（前台）
hermes dashboard --port 9119 --no-open &      # 后台启动
hermes dashboard --status                     # 查看状态
hermes dashboard --stop                       # 停止 Dashboard

# 配置管理
hermes config                               # 查看配置
hermes config set model.provider openrouter  # 设置 Provider
hermes config edit                            # 编辑配置文件

# 会话管理
hermes sessions list                          # 列出会话
hermes sessions browse                        # 交互式选择会话

# Gateway 管理
hermes gateway status                         # Gateway 状态
hermes gateway start                          # 启动 Gateway
hermes gateway restart                        # 重启 Gateway

# 检查诊断
hermes doctor                                 # 系统完整性检查
hermes config check                           # 配置检查
hermes logs                                   # 查看日志
```

---

## 总结

| 项目 | 说明 |
|------|------|
| **一键启动** | `hermes dashboard` |
| **访问地址** | `http://localhost:9119` |
| **核心入口** | Dashboard Web UI |
| **替代方案** | CLI / Telgram / Discord / Slack 等 |
| **安全性** | 仅限本地或需 HTTPS + Auth |
| **文档** | Dashboard 内置或 `hermes-agent.nousresearch.com/docs` |

**部署完成！🎉** 你现在已经可以直接通过浏览器管理和操作 Hermes Agent 了。
