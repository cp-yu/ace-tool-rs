# ace-tool-rs

English | [简体中文](README-zh-CN.md)

A high-performance MCP (Model Context Protocol) server for codebase indexing and semantic search, written in Rust.

## Overview

ace-tool-rs provides:

- Real-time codebase indexing with incremental updates
- Semantic code search using natural-language queries
- MCP JSON-RPC 2.0 server over stdio
- Adaptive upload strategy based on runtime metrics
- Multi-language and multi-encoding file support
- Respect for `.gitignore`, `.aceignore`, and common exclude patterns

## Installation

### Quick Start

```bash
npx ace-tool-rs --base-url <API_URL> --token <AUTH_TOKEN>
```

### From Source

```bash
git clone https://github.com/missdeer/ace-tool-rs.git
cd ace-tool-rs
cargo build --release
```

The binary will be available at `target/release/ace-tool-rs`.

## Requirements

- Rust 1.70 or later
- An API endpoint for the indexing service
- An authentication token

## Usage

### Command Line

```bash
ace-tool-rs --base-url <API_URL> --token <AUTH_TOKEN>
```

### Arguments

| Argument | Description |
|----------|-------------|
| `--base-url` | API base URL for the indexing service |
| `--token` | Authentication token for API access |
| `--transport` | Transport framing: `auto` (default), `lsp`, `line` |
| `--upload-timeout` | Override upload timeout in seconds |
| `--upload-concurrency` | Override upload concurrency |
| `--no-adaptive` | Disable adaptive strategy |
| `--index-only` | Index current directory and exit |
| `--max-lines-per-blob` | Maximum lines per blob chunk (default: 800) |
| `--retrieval-timeout` | Search retrieval timeout in seconds |

### Environment Variables

| Variable | Description |
|----------|-------------|
| `RUST_LOG` | Set log level, for example `info`, `debug`, or `warn` |

### Example

```bash
RUST_LOG=debug ace-tool-rs --base-url https://api.example.com --token your-token-here
```

## MCP Integration

### Codex CLI

Add to `~/.codex/config.toml`:

```toml
[mcp_servers.ace-tool]
command = "npx"
args = ["-y", "ace-tool-rs", "--base-url", "<API_URL>", "--token", "<AUTH_TOKEN>"]
```

### Claude Code Permissions

Add permission in `settings.local.json`:

```json
{
  "permissions": {
    "allow": [
      "mcp__ace-tool__search_context"
    ]
  }
}
```

### Available Tools

#### `search_context`

Search the codebase using natural-language queries.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `project_root_path` | string | Yes | Absolute path to the project root directory |
| `query` | string | Yes | Natural-language description of the code to find |

Example queries:

- `Where is the function that handles user authentication?`
- `What tests are there for the login functionality?`
- `How is the database connected to the application?`

## Supported File Types

ace-tool-rs indexes common source, config, and text formats including:

- Programming languages: `.rs`, `.py`, `.js`, `.ts`, `.go`, `.java`, `.cpp`, `.c`, `.cs`, `.rb`, `.php`, `.swift`, `.kt`, `.scala`
- Web files: `.html`, `.css`, `.scss`, `.vue`, `.svelte`, `.astro`
- Config and data: `.json`, `.yaml`, `.yml`, `.toml`, `.xml`, `.ini`, `.conf`, `.md`, `.txt`
- Special files: `Makefile`, `Dockerfile`, `Jenkinsfile`, `.gitignore`, `.env.example`, `requirements.txt`

## Architecture

```text
ace-tool-rs/
├── src/
│   ├── main.rs          # Entry point and CLI
│   ├── lib.rs           # Library exports
│   ├── config.rs        # Configuration and upload strategies
│   ├── http_logger.rs   # HTTP request/response logging
│   ├── index/
│   │   ├── mod.rs
│   │   └── manager.rs   # Core indexing and search logic
│   ├── mcp/
│   │   ├── mod.rs
│   │   ├── server.rs    # MCP server implementation
│   │   └── types.rs     # JSON-RPC types
│   ├── strategy/
│   │   ├── mod.rs
│   │   ├── adaptive.rs  # AIMD algorithm implementation
│   │   └── metrics.rs   # EWMA and runtime metrics
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

## Development

```bash
cargo build
cargo build --release
cargo test
cargo clippy --all-targets --all-features -- -D warnings
```
