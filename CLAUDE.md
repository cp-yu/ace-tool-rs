# Repository Workflow

- 使用中文沟通。
- 使用 `mcp__ace_tool__search_context` 获取项目上下文。
- 代码修改理由追踪：代码即 How，注释即 Why。

## Git Remotes

- `upstream`: 原来的 `origin`，用于跟踪上游仓库 `https://github.com/missdeer/ace-tool-rs.git`
- `origin`: 个人仓库，用于提交自己的修改 `git@github.com:cp-yu/ace-tool-rs.git`

## Daily Git Flow

- 获取上游更新：`git fetch upstream`
- 合并上游更新到当前分支：`git merge upstream/master`
- 推送自己的修改到个人仓库：`git push origin master`

## Local Git Config

- 当前分支 `master` 跟踪 `upstream/master`
- 当前分支默认推送远端为 `origin`
