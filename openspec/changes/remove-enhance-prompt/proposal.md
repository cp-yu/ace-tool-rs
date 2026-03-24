## Why

上游 Augment 服务不再提供 prompt-enhancer API 功能，导致 enhance_prompt 工具无法正常工作。需要移除该功能以避免用户困惑和维护负担。

## What Changes

- **BREAKING** 移除 `enhance_prompt` MCP 工具及其所有相关组件
- 移除整个 `enhancer` 模块（包括 prompt enhancer、Web UI 服务器、模板等）
- 移除 CLI 中的 `--enhance-prompt` 参数
- 清理配置中与 enhance_prompt 相关的字段
- 移除所有相关测试文件

## Capabilities

### New Capabilities

无新增功能。

### Modified Capabilities

- `mcp-tool-registration`: MCP 工具列表将不再包含 `enhance_prompt` 工具，仅保留 `search_context` 工具

## Impact

**受影响的代码**：
- `src/enhancer/` - 整个目录将被删除
- `src/tools/enhance_prompt.rs` - 工具定义文件将被删除
- `src/tools/mod.rs` - 移除 enhance_prompt 导出
- `src/mcp/server.rs` - 移除工具注册和调用逻辑
- `src/main.rs` - 移除 CLI 参数处理和导入
- `src/config.rs` - 移除相关配置字段
- `src/lib.rs` - 移除 enhancer 模块导出
- `tests/enhance_prompt_test.rs` - 删除
- `tests/enhancer_server_test.rs` - 删除
- `tests/prompt_enhancer_test.rs` - 删除
- `tests/templates_test.rs` - 删除

**API 变更**：
- MCP `tools/list` 响应将不再包含 `enhance_prompt` 工具
- 移除 `--enhance-prompt` CLI 参数

**依赖项**：
- 可能可以移除一些仅用于 enhancer 的依赖项（需在 design 阶段确认）

**系统影响**：
- 用户将无法再使用 enhance_prompt 功能
- 现有使用该工具的集成将失败（需在文档中说明）
