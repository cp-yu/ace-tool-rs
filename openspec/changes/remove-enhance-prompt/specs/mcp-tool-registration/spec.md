## REMOVED Requirements

### Requirement: enhance_prompt tool registration

**Reason**: 上游 Augment 服务不再提供 prompt-enhancer API，该工具无法正常工作

**Migration**: 用户应移除对 `enhance_prompt` MCP 工具的依赖。该工具将从 MCP `tools/list` 响应中完全移除。

The system previously registered an `enhance_prompt` MCP tool that allowed clients to enhance user prompts with codebase context. This tool is now removed.

#### Scenario: enhance_prompt tool not in tools list
- **WHEN** MCP client calls `tools/list` method
- **THEN** response SHALL NOT include `enhance_prompt` in the tools array
- **AND** response SHALL only include `search_context` tool

#### Scenario: enhance_prompt tool call fails
- **WHEN** MCP client attempts to call `enhance_prompt` tool via `tools/call`
- **THEN** server SHALL return error response with "Method not found" or "Unknown tool"

### Requirement: enhance_prompt configuration options

**Reason**: 配置选项仅服务于已移除的 enhance_prompt 功能

**Migration**: 用户应从配置中移除以下选项：
- `--no-webbrowser-enhance-prompt`
- `--force-xdg-open`
- `--webui-addr`
- `--enhance-prompt` CLI 参数

The system previously supported configuration options for controlling enhance_prompt behavior. These options are now removed.

#### Scenario: enhance_prompt CLI parameter removed
- **WHEN** user runs ace-tool with `--enhance-prompt` parameter
- **THEN** CLI SHALL return error indicating unknown parameter

#### Scenario: enhance_prompt config fields removed
- **WHEN** system initializes Config struct
- **THEN** Config SHALL NOT contain `no_webbrowser_enhance_prompt` field
- **AND** Config SHALL NOT contain `force_xdg_open` field
- **AND** Config SHALL NOT contain `webui_addr` field

## MODIFIED Requirements

### Requirement: MCP server initialization

The MCP server SHALL initialize with only the `search_context` tool registered, without any enhance_prompt related components.

#### Scenario: Server starts successfully without enhancer
- **WHEN** MCP server starts
- **THEN** server SHALL initialize successfully
- **AND** server SHALL NOT attempt to load enhancer module
- **AND** server SHALL NOT start Web UI server

#### Scenario: tools/list returns only search_context
- **WHEN** MCP client requests available tools
- **THEN** response SHALL contain exactly one tool: `search_context`
- **AND** response SHALL NOT contain `enhance_prompt`
