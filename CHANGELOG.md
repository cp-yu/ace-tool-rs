# Changelog

## 0.2.0 - 2026-03-19

### Breaking Changes

- Removed the `enhance_prompt` MCP tool.
- Removed the `--enhance-prompt`, `--no-webbrowser-enhance-prompt`, `--force-xdg-open`, and `--webui-addr` CLI options.
- `tools/list` now returns only `search_context`.

### Notes

- This removal follows the upstream prompt-enhancer API deprecation and removes the unusable enhancer implementation from the codebase.
- Existing clients should stop invoking `enhance_prompt` and rely on `tools/list` for current tool discovery.

### Dependency Review Follow-up

The following dependencies appear to be enhancer/Web UI leftovers after this change and should be reviewed in a follow-up cleanup change:

- `hyper`
- `hyper-util`
- `http-body-util`
- `open`
