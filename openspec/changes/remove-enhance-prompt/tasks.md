## 1. 删除测试文件

- [x] 1.1 删除 tests/enhance_prompt_test.rs
- [x] 1.2 删除 tests/enhancer_server_test.rs
- [x] 1.3 删除 tests/prompt_enhancer_test.rs
- [x] 1.4 删除 tests/templates_test.rs
- [x] 1.5 验证 `cargo test` 编译通过

## 2. 移除 CLI 参数处理

- [x] 2.1 从 src/main.rs 中删除 `--enhance-prompt` 参数定义
- [x] 2.2 从 src/main.rs 中删除 enhance-prompt 模式处理逻辑
- [x] 2.3 从 src/main.rs 中删除 enhancer 相关导入
- [x] 2.4 验证 `cargo build` 编译通过

## 3. 移除 MCP 工具注册

- [x] 3.1 从 src/mcp/server.rs 中删除 `enhance_prompt` 工具导入
- [x] 3.2 从 src/mcp/server.rs 的 `handle_list_tools` 中移除 enhance_prompt 工具注册逻辑
- [x] 3.3 从 src/mcp/server.rs 的 `handle_call_tool` 中移除 enhance_prompt 工具调用逻辑
- [x] 3.4 删除 `is_enhance_prompt_enabled()` 函数（如果存在）
- [x] 3.5 验证 MCP server 仍能正常启动

## 4. 删除工具实现

- [x] 4.1 删除 src/tools/enhance_prompt.rs 文件
- [x] 4.2 从 src/tools/mod.rs 中删除 `pub mod enhance_prompt;`
- [x] 4.3 从 src/tools/mod.rs 中删除 `pub use enhance_prompt::EnhancePromptTool;`
- [x] 4.4 验证 tools 模块编译通过

## 5. 删除 enhancer 模块

- [x] 5.1 删除 src/enhancer/prompt_enhancer.rs
- [x] 5.2 删除 src/enhancer/server.rs
- [x] 5.3 删除 src/enhancer/templates.rs
- [x] 5.4 删除 src/enhancer/mod.rs
- [x] 5.5 删除 src/enhancer/ 目录
- [x] 5.6 验证目录已完全删除

## 6. 清理库导出

- [x] 6.1 从 src/lib.rs 中删除 `pub mod enhancer;`
- [x] 6.2 从 src/lib.rs 中删除 `pub use enhancer::PromptEnhancer;`
- [x] 6.3 验证库编译通过

## 7. 清理配置字段

- [x] 7.1 从 src/config.rs 的 ConfigOptions 中删除 `no_webbrowser_enhance_prompt` 字段
- [x] 7.2 从 src/config.rs 的 ConfigOptions 中删除 `force_xdg_open` 字段
- [x] 7.3 从 src/config.rs 的 ConfigOptions 中删除 `webui_addr` 字段
- [x] 7.4 从 src/config.rs 的 Config 中删除对应的 3 个字段
- [x] 7.5 从 Config::new() 中删除这些字段的初始化代码
- [x] 7.6 删除 Config::minimal_for_enhance_prompt() 方法（如果存在）
- [x] 7.7 验证 config 模块编译通过

## 8. 全局验证

- [x] 8.1 运行 `cargo build --release` 确保编译通过
- [x] 8.2 运行 `cargo test` 确保所有测试通过
- [x] 8.3 运行 `cargo clippy` 检查无警告
- [x] 8.4 使用 `grep -r "enhance_prompt\|enhancer" src/` 确认无遗漏引用
- [x] 8.5 手动测试 MCP server 启动和 tools/list 响应

## 9. 依赖项审查（可选）

- [x] 9.1 检查 Cargo.toml 中是否有仅用于 enhancer 的依赖项
- [x] 9.2 标记可能可以移除的依赖项（不在此变更中删除）
- [x] 9.3 在 CHANGELOG 中记录需要后续审查的依赖项

## 10. 文档更新

- [x] 10.1 更新 README.md，从工具列表中移除 enhance_prompt
- [x] 10.2 更新 README-zh-CN.md，从工具列表中移除 enhance_prompt
- [x] 10.3 在 CHANGELOG.md 中添加 BREAKING CHANGE 说明
- [x] 10.4 更新版本号为 0.2.0（主版本变更）
