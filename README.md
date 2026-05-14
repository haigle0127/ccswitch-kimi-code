# cswitch-kimi-code

让 Codex CLI / Codex 桌面客户端通过 **Kimi Code**（Kimi K2.6）模型运行。

Codex 使用 Responses API 协议，而 Kimi Code 只提供 Chat Completions API。本项目在本地启动一个协议翻译代理，在两者之间无缝转换。

## 架构

```
Codex 客户端 ──Responses API──▶ index.js :11436 ──Chat API──▶ api.kimi.com/coding
                                  协议翻译
```

## 前置条件

- Node.js >= 18
- Kimi Code API Key（[获取地址](https://www.kimi.com/code/docs/)）

## 快速开始

### 1. 安装依赖

```bash
npm install
```

### 2. 配置 API Key

编辑 `.env`：

```
api_key=sk-kimi-your-api-key
```

### 3. 启动代理服务

```bash
npm start
```

输出：

```
Codex → kimi Proxy
  http://127.0.0.1:11436/v1/responses
  Model: kimi-k2.6
```

### 4. 配置 CCSwitch

CCSwitch 桌面应用中，API 地址填写：

```
http://127.0.0.1:11436/v1
```

CCSwitch 会引导 Codex 客户端将请求发送到本地代理。

## Codex CLI 用户

如果直接使用 Codex CLI（不通过 CCSwitch），编辑 `~/.codex/config.toml`：

```toml
[model_providers.kimi]
base_url = "http://127.0.0.1:11436/v1"
wire_api = "responses"
requires_openai_auth = false
stream_idle_timeout_ms = 300000

[profiles.kimi-k2.6]
model_provider = "kimi"
model_name = "kimi-k2.6"
context_window = 1000000
max_output_tokens = 32768

[profiles.kimi-k2.6.features]
tool_search = false
tool_search_always_defer_mcp_tools = false
```

使用：

```bash
codex --profile kimi-k2.6
```

## 环境变量

| 变量 | 默认值 | 说明 |
|------|--------|------|
| `api_key` | - | Kimi Code API Key（必填，`sk-kimi-` 前缀） |
| `KIMI_PROXY_HOST` | `127.0.0.1` | 代理监听地址 |
| `KIMI_PROXY_PORT` | `11436` | 代理监听端口 |
| `KIMI_MODEL` | `kimi-k2.6` | 默认模型 |

## 功能

- **协议翻译**：Responses API ↔ Chat Completions 双向转换
- **工具过滤**：Kimi 限制 128 个工具，超出时自动按域名关键词优先级裁剪
- **命名空间处理**：自动处理 MCP 工具命名空间（`gmail___search_emails` 等）
- **角色映射**：自动将 OpenAI `developer` role 映射为 `system`
- **内容格式翻译**：`input_text` / `output_text` → `text`

## 鸣谢

本项目架构与实现灵感来自 [ccswitch-deepseek](https://github.com/liuzhengming/ccswitch-deepseek)，感谢原作者的开源贡献。

## License

ISC
