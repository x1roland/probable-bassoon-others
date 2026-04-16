# Cron 任务档案

> 由 ops 维护，最后更新：2026-04-14
> ✅ 2026-04-14 巡检：已清理已删除的 Release 追踪任务档案

## 汇总

| # | 名称 | 调度 | 状态 | 脚本/说明 | 通知 |
|---|------|------|------|----------|------|
| 1 | **Cron 每日巡检** | 每天 04:23 | ✅ | 状态检查+配置变更检测+档案同步 | 飞书（仅异常或变更时） |
| 2 | 遗留问题检查 | 每 ~71min | ✅ | `scripts/check-issues.ps1` | Telegram（仅异常时） |
| 3 | folo-health-check | 每小时 ×2（:07,:37） | ✅ | `projects/folo/health.ps1` | Telegram（仅异常时） |
| 4 | 每日凌晨记日记 | 每天 02:08 | ✅ | 遍历所有 agent 写日记 | 无 |
| 5 | Workspace Git 备份 | 每天 03:17 | ✅ | `scripts/backup-workspace.bat` | 无 |
| 6 | 配置与凭证备份 | 每天 03:34 | ✅ | `openclaw backup` + cleanup 脚本 | 无 |


## 详情

### 1. Cron 每日巡检 ⭐
- **ID:** b29205c7-9a7d-45e1-a148-46dffa642470
- **模型:** zai/glm-5.1
- **类型:** isolated / agentTurn
- **通知:** 飞书（target=ou_eb00ece2dfc03003d2db37d5acbd41ec）
- **说明:** 每日校验任务状态+配置变更检测，自动同步 cron-registry.md，异常或变更时通知
- **✅ 当前状态:** 正常，consecutiveErrors=0
- **修复记录（2026-04-11）:** 之前因未明确指定飞书 target 导致 isolated session 发消息失败，已修正 prompt 明确指定 channel+target

### 2. 遗留问题检查
- **ID:** 91b92420-7cc8-48ce-9b96-28b038582aed
- **模型:** zai/glm-5.1
- **脚本:** `C:\Users\fanci\.openclaw\scripts\check-issues.ps1`
- **通知:** Telegram → 5544196119（仅在有问题时）
- **类型:** isolated / agentTurn
- **说明:** 检查 issues 和 tasks，有遗留则通知，无则静默

### 3. folo-health-check
- **ID:** 5c5c57d9-326f-49ce-9c34-abf5a85026ea
- **模型:** zai/glm-5.1
- **脚本:** `C:\Users\fanci\projects\folo\health.ps1`
- **通知:** Telegram → 5544196119（仅在 recovered/failed 时）
- **类型:** isolated / agentTurn
- **说明:** folo 服务健康检查，ok 时静默

### 4. 每日凌晨记日记
- **ID:** a5e71713-c3fb-466e-ab9d-3d8b81c74905
- **模型:** openai-codex/gpt-5.4
- **类型:** isolated / agentTurn
- **说明:** 遍历所有 agent（换 GPT-5.4 解决 5 agent 上下文溢出问题）（main、coder、tester、ops、social），读取各自主会话历史，写入各 workspace 下的 memory/YYYY-MM-DD.md

### 5. Workspace Git 备份
- **模型:** zai/glm-5-turbo
- **ID:** 74d31fe4-e274-413c-99b4-eb9593a12e7c
- **脚本:** `~/.openclaw/scripts/backup-workspace.bat`
- **类型:** isolated / agentTurn
- **说明:** workspace 推送到 GitHub 私有仓库

### 6. 配置与凭证备份
- **模型:** zai/glm-5-turbo
- **ID:** f6fd082a-03c0-4118-b809-11b653ab4d1b
- **脚本:** `openclaw backup create` + `~/.openclaw/scripts/cleanup-backups.ps1`
- **类型:** isolated / agentTurn
- **说明:** OpenClaw 配置备份，保留最近 15 天 + 每月 1/16 日快照

### 7. ~~OpenClaw Release 追踪~~ (已删除)
- **ID:** 875e85e4-b1fd-470e-bec2-96f1715f8312
- **状态:** ❌ 已从系统删除（2026-04-14 确认）

**巡检日志 - 2026-04-15 20:23 UTC (2026-04-16 04:23 CST)**
- ⚠️ 每日巡检自身上次超时（consecutiveErrors=1），本次正常运行中
- ⚠️ 每日凌晨记日记上次超时（consecutiveErrors=1, 300s timeout）
- ✅ 其余 4 个任务 consecutiveErrors=0
- 📊 任务匹配度：6/6 一致，无新增/删除/禁用任务

## 维护规则

1. 任何新增/修改/删除任务前，先向 THE ONE 确认
2. 变更后同步更新本档案
3. 每周巡检一次运行状态，异常主动报告
4. 已禁用任务超过 30 天无计划时提醒清理

---

**巡检日志 - 2026-04-12 04:23 UTC**
- ✅ 所有启用任务状态正常，consecutiveErrors=0
- ⚠️ 发现：Release 追踪任务已禁用 32.7 天，超过 30 天阈值（已超期2.7天）
- 📊 任务状态：6/7 任务正常运行，1个任务需要处理
- 🔧 任务匹配度：100%（7/7 任务一致）
- 🔔 通知状态：多个任务存在通知失败（lastDeliveryStatus="not-delivered"）