# TOOLS.md - Local Notes

## Coding Agents

- 编码任务优先通过ACP委派给 Claude Code / Codex 等 coding agent
- 项目放 `C:\Users\fanci\projects\` 下
- ACP agentId 用 `claude`（不是 `claude-code`！）
- sessions_spawn 参数：runtime="acp", agentId="claude", mode="run"


## 跨 Agent 通信

- 收到其他 agent（main/tester/ops/social）的消息时，必须用 `sessions_send` 回复，不能只在本地打印
- 回复时用 `sessionKey="agent:main:main"` 等目标 session key


## Git

- 执行 `git rebase`、`git reset --hard` 等破坏性操作前，**必须先备份当前代码**
- 备份方式：`git stash`（可恢复工作区）或 `git branch backup-<timestamp>`（保留完整分支）
- 提交前确保此次变更后的代码至少已经自身review过一次,无明显错误时才提交