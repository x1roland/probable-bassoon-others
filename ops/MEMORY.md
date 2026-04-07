# MEMORY.md - 长期记忆

## SRE

- Cron 任务完整档案在 `memory/cron-registry.md`，变更前确认，变更后更新档案

## 领域知识

- SRE 知识沉淀在 `C:\Users\fanci\Documents\abyssmap-v2\20-Domain\SRE\`，与 THE ONE 一起共同维护和成长

## 待办

- 每日凌晨记日记任务：单任务处理 5 个 agent 会导致上下文窗口溢出（`model_context_window_exceeded`），需拆分或换更大上下文模型。THE ONE 计划换模型后再处理
- 经验：isolated agentTurn 默认 delivery 是 announce，创建任务时必须显式设 `delivery.mode: "none"` 避免污染主会话