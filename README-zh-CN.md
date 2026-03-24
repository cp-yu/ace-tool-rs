# ace-tool-rs

[English](README.md) | 简体中文

一个使用 Rust 编写的高性能 MCP（模型上下文协议）服务器，用于代码库索引和语义搜索。

## 概述

ace-tool-rs 提供：

- 支持增量更新的实时代码库索引
- 基于自然语言查询的语义代码搜索
- 基于 stdio 的 MCP JSON-RPC 2.0 服务端
- 基于运行时指标的自适应上传策略
- 多语言、多编码文件支持
- 遵循 `.gitignore`、`.aceignore` 和常见排除规则

## 安装

### 快速开始

```bash
npx ace-tool-rs --base-url <API_URL> --token <AUTH_TOKEN>
```

### 从源码构建

```bash
git clone https://github.com/missdeer/ace-tool-rs.git
cd ace-tool-rs
cargo build --release
```

二进制文件位于 `target/release/ace-tool-rs`。

## 环境要求

- Rust 1.70 或更高版本
- 索引服务 API 端点
- 认证令牌

## 使用方法

### 命令行

```bash
ace-tool-rs --base-url <API_URL> --token <AUTH_TOKEN>
```

### 参数

| 参数 | 描述 |
|------|------|
| `--base-url` | 索引服务的 API 基础 URL |
| `--token` | API 访问认证令牌 |
| `--transport` | 传输帧格式：`auto`（默认）、`lsp`、`line` |
| `--upload-timeout` | 覆盖上传超时时间（秒） |
| `--upload-concurrency` | 覆盖上传并发度 |
| `--no-adaptive` | 禁用自适应策略 |
| `--index-only` | 仅索引当前目录并退出 |
| `--max-lines-per-blob` | 每个 blob 的最大行数（默认 800） |
| `--retrieval-timeout` | 搜索检索超时时间（秒） |

### 环境变量

| 变量 | 描述 |
|------|------|
| `RUST_LOG` | 设置日志级别，例如 `info`、`debug`、`warn` |

### 示例

```bash
RUST_LOG=debug ace-tool-rs --base-url https://api.example.com --token your-token-here
```

## MCP 集成

### Codex CLI

在 `~/.codex/config.toml` 中添加：

```toml
[mcp_servers.ace-tool]
command = "npx"
args = ["-y", "ace-tool-rs", "--base-url", "<API_URL>", "--token", "<AUTH_TOKEN>"]
```

### Claude Code 权限

在 `settings.local.json` 中添加权限：

```json
{
  "permissions": {
    "allow": [
      "mcp__ace-tool__search_context"
    ]
  }
}
```

### 可用工具

#### `search_context`

使用自然语言查询搜索代码库。

| 参数 | 类型 | 必需 | 描述 |
|------|------|------|------|
| `project_root_path` | string | 是 | 项目根目录的绝对路径 |
| `query` | string | 是 | 要查找代码的自然语言描述 |

示例查询：

- `处理用户认证的函数在哪里？`
- `登录功能有哪些测试？`
- `数据库是如何连接到应用程序的？`

## 支持的文件类型

ace-tool-rs 支持常见源码、配置和文本文件，包括：

- 编程语言：`.rs`、`.py`、`.js`、`.ts`、`.go`、`.java`、`.cpp`、`.c`、`.cs`、`.rb`、`.php`、`.swift`、`.kt`、`.scala`
- Web 文件：`.html`、`.css`、`.scss`、`.vue`、`.svelte`、`.astro`
- 配置和数据：`.json`、`.yaml`、`.yml`、`.toml`、`.xml`、`.ini`、`.conf`、`.md`、`.txt`
- 特殊文件：`Makefile`、`Dockerfile`、`Jenkinsfile`、`.gitignore`、`.env.example`、`requirements.txt`

## 架构

```text
ace-tool-rs/
├── src/
│   ├── main.rs          # 入口和 CLI
│   ├── lib.rs           # 库导出
│   ├── config.rs        # 配置和上传策略
│   ├── http_logger.rs   # HTTP 请求/响应日志
│   ├── index/
│   │   ├── mod.rs
│   │   └── manager.rs   # 核心索引与搜索逻辑
│   ├── mcp/
│   │   ├── mod.rs
│   │   ├── server.rs    # MCP 服务端实现
│   │   └── types.rs     # JSON-RPC 类型
│   ├── strategy/
│   │   ├── mod.rs
│   │   ├── adaptive.rs  # AIMD 算法实现
│   │   └── metrics.rs   # EWMA 和运行时指标
│   ├── tools/
│   │   ├── mod.rs
│   │   └── search_context.rs
│   └── utils/
│       ├── mod.rs
│       ├── path_normalizer.rs
│       └── project_detector.rs
└── tests/
    ├── config_test.rs
    ├── http_logger_test.rs
    ├── index_test.rs
    ├── mcp_server_test.rs
    ├── mcp_test.rs
    ├── path_normalizer_test.rs
    ├── tools_test.rs
    └── utils_test.rs
```

## 开发

```bash
cargo build
cargo build --release
cargo test
cargo clippy --all-targets --all-features -- -D warnings
```
