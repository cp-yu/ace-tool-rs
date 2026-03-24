## Context

ace-tool 项目最初集成了 Augment 的 prompt-enhancer API，通过 `enhance_prompt` MCP 工具提供提示词增强功能。该功能包括：
- 完整的 enhancer 模块（API 调用、Web UI 服务器、模板系统）
- MCP 工具注册和调用逻辑
- CLI 独立模式支持（`--enhance-prompt` 参数）
- 相关配置选项（`no_webbrowser_enhance_prompt`、`webui_addr` 等）

由于上游 Augment 服务不再提供该 API，该功能已无法正常工作，需要完全移除以避免用户困惑和维护负担。

当前状态：
- `src/enhancer/` 包含 4 个文件（prompt_enhancer.rs, server.rs, mod.rs, templates.rs）
- `src/tools/enhance_prompt.rs` 实现 MCP 工具接口
- `src/mcp/server.rs` 中有条件注册逻辑（`is_enhance_prompt_enabled()`）
- `src/config.rs` 包含 3 个相关配置字段
- 4 个测试文件专门测试该功能

## Goals / Non-Goals

**Goals:**
- 完全移除 enhance_prompt 功能及其所有组件
- 清理所有相关配置、CLI 参数和测试
- 保持 search_context 工具正常工作
- 确保编译通过且无未使用代码警告
- 识别并移除仅用于 enhancer 的依赖项

**Non-Goals:**
- 不提供替代的 prompt enhancement 功能
- 不保留任何"禁用"开关（直接删除，不是隐藏）
- 不修改 search_context 工具的任何逻辑

## Decisions

### 决策 1：完全删除 vs 保留框架

**选择：完全删除**

理由：
- 上游 API 已不可用，保留代码无意义
- 维护死代码会增加技术债务
- 用户无法使用该功能，保留会造成困惑

替代方案：
- 保留代码但禁用：增加维护负担，且用户可能尝试启用后发现不工作
- 替换为其他实现：超出当前变更范围，可作为未来独立功能

### 决策 2：删除顺序

**选择：自底向上删除（先删除依赖者，后删除被依赖者）**

删除顺序：
1. 测试文件（无依赖）
2. CLI 参数处理（main.rs）
3. MCP 工具注册（mcp/server.rs）
4. 工具实现（tools/enhance_prompt.rs）
5. 工具模块导出（tools/mod.rs）
6. enhancer 模块（src/enhancer/）
7. lib.rs 导出
8. 配置字段（config.rs）

理由：
- 避免编译错误累积
- 每一步都保持代码可编译状态
- 便于验证和回滚

### 决策 3：依赖项清理

**选择：识别但不在此变更中删除**

理由：
- 需要仔细分析 Cargo.toml 中哪些依赖仅用于 enhancer
- 某些依赖可能被其他模块共享（如 reqwest、tokio）
- 依赖清理应作为独立的优化任务，避免引入风险

在 tasks.md 中标记需要检查的依赖项。

### 决策 4：配置字段处理

**选择：删除所有相关配置字段**

需要删除的字段：
- `ConfigOptions::no_webbrowser_enhance_prompt`
- `ConfigOptions::force_xdg_open`（仅用于 enhancer Web UI）
- `ConfigOptions::webui_addr`（仅用于 enhancer Web UI）
- `Config::no_webbrowser_enhance_prompt`
- `Config::force_xdg_open`
- `Config::webui_addr`

理由：
- 这些字段仅服务于 enhance_prompt 功能
- 删除后不影响 search_context 工具

## Risks / Trade-offs

### 风险 1：破坏现有集成

**风险**：使用 `enhance_prompt` 工具的用户集成将失败

**缓解措施**：
- 在 CHANGELOG 中明确标记为 BREAKING CHANGE
- 在 README 中更新工具列表，移除 enhance_prompt
- 考虑在下一个主版本发布（如 0.2.0）

### 风险 2：遗漏清理

**风险**：可能遗漏某些引用或配置

**缓解措施**：
- 使用 `grep -r "enhance_prompt\|enhancer"` 全局搜索
- 编译后检查 unused 警告
- 运行完整测试套件

### 风险 3：共享依赖误删

**风险**：删除被其他模块使用的依赖项

**缓解措施**：
- 不在此变更中删除依赖项
- 仅在 tasks.md 中标记需要审查的依赖
- 后续通过编译测试验证

## Migration Plan

### 部署步骤

1. 合并代码到主分支
2. 更新版本号为 0.2.0（主版本变更）
3. 发布新版本到 crates.io
4. 更新文档和 README

### 回滚策略

如果发现问题：
- Git revert 该变更的 commit
- 或从 git history 恢复删除的文件

### 用户迁移

**受影响用户**：使用 `enhance_prompt` MCP 工具的用户

**迁移路径**：
- 无直接迁移路径（功能已移除）
- 建议用户使用其他 prompt enhancement 工具或服务
- 在文档中说明移除原因

## Open Questions

1. **是否需要提供弃用警告期？**
   - 当前决策：不需要，因为功能已不可用
   - 如果上游 API 仍可用，应先标记为 deprecated

2. **是否保留 Web UI 服务器框架用于未来功能？**
   - 当前决策：不保留，未来如需要可重新实现
   - 理由：YAGNI 原则，避免维护未使用的代码

3. **是否需要在 MCP 协议层面通知客户端工具已移除？**
   - 当前决策：不需要，客户端通过 `tools/list` 自动发现可用工具
   - MCP 协议本身支持动态工具列表
