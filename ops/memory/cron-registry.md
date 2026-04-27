# Cron 任务档案

> 由 ops 维护，最后更新：2026-04-27
> ✅ 2026-04-27 巡检：所有启用任务正常；日记任务已恢复；Release 追踪档案状态修正
> 🔧 2026-04-27 15:54 CST：按 THE ONE 要求，将“每日凌晨记日记”模型切换为 deepseek/deepseek-v4-pro
> 🔧 2026-04-27 22:18 CST：接手 folo-push 定时系统，启用 4 个任务并纳入维护

## 汇总

| # | 名称 | 调度 | 状态 | 脚本/说明 | 通知 |
|---|------|------|------|----------|------|
| 1 | **Cron 每日巡检** | 每天 04:23 | ✅ | 状态检查+配置变更检测+档案同步 | 飞书(仅异常或变更时) |
| 2 | 遗留问题检查 | 每 ~71min | ✅ | `scripts/check-issues.ps1` | Telegram(仅异常时) |
| 3 | folo-health-check | 每小时 ×2(:07,:37) | ✅ | `projects/folo/health.ps1` | Telegram(仅异常时) |
| 4 | 每日凌晨记日记 | 每天 02:08 | ✅ | 遍历所有 agent 写日记 | 无 |
| 5 | Workspace Git 备份 | 每天 03:17 | ✅ | `scripts/backup-workspace.bat` | 无 |
| 6 | OpenClaw 配置与凭证备份 | 每天 03:34 | ✅ | `openclaw backup` + cleanup 脚本 | 无 |
| 8 | **folo-morning-digest** | 每天 07:23 | ✅ | folo 晨报，补收昨夜+今晨 | 飞书→Telegram |
| 9 | **folo-instant-check** | 08:11/12:11/16:11/20:11 | ✅ | folo 即时推送检测 | 飞书→Telegram |
| 10 | **folo-daily-digest** | 每天 19:33 | ✅ | folo 晚报 | 飞书→Telegram |
| 11 | **folo-weekly-digest** | 每周日 07:11 | ✅ | folo 周报 | 飞书→Telegram |


## 详情

### 1. Cron 每日巡检 ⭐
- **ID:** b29205c7-9a7d-45e1-a148-46dffa642470
- **模型:** zai/glm-5.1
- **类型:** isolated / agentTurn
- **通知:** 飞书(target=ou_eb00ece2dfc03003d2db37d5acbd41ec)
- **说明:** 每日校验任务状态+配置变更检测,自动同步 cron-registry.md,异常或变更时通知
- **⚠️ 当前状态:** consecutiveErrors=1,上次超时(300s)
- **修复记录(2026-04-11):** 之前因未明确指定飞书 target 导致 isolated session 发消息失败,已修正 prompt 明确指定 channel+target

### 2. 遗留问题检查
- **ID:** 91b92420-7cc8-48ce-9b96-28b038582aed
- **模型:** zai/glm-5.1
- **脚本:** `C:\Users\fanci\.openclaw\scripts\check-issues.ps1`
- **通知:** Telegram → 5544196119(仅在有问题时)
- **类型:** isolated / agentTurn
- **说明:** 检查 issues 和 tasks,有遗留则通知,无则静默

### 3. folo-health-check
- **ID:** 5c5c57d9-326f-49ce-9c34-abf5a85026ea
- **模型:** zai/glm-5.1
- **脚本:** `C:\Users\fanci\projects\folo\health.ps1`
- **通知:** Telegram → 5544196119(仅在 recovered/failed 时)
- **类型:** isolated / agentTurn
- **说明:** folo 服务健康检查,ok 时静默

### 4. 每日凌晨记日记
- **ID:** a5e71713-c3fb-466e-ab9d-3d8b81c74905
- **模型:** deepseek/deepseek-v4-pro
- **类型:** isolated / agentTurn
- **说明:** 遍历所有 agent(main、coder、tester、ops、social),读取各自主会话历史,写入各 workspace 下的 memory/YYYY-MM-DD.md
- **变更记录:**
  - 2026-04-27 15:54 CST：按 THE ONE 要求，从 openai-codex/gpt-5.4 切换到 deepseek/deepseek-v4-pro
  - 之前曾切到 GPT-5.4 以缓解 5 agent 场景下的上下文溢出问题，后续需继续观察 deepseek-v4-pro 表现

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
- **说明:** OpenClaw 配置备份,保留最近 15 天 + 每月 1/16 日快照

### 8. folo-morning-digest
- **ID:** 78649a76-470b-4d09-b5f9-3e8a74fddf93
- **模型:** zai/glm-5.1
- **类型:** isolated / agentTurn (thinking: adaptive)
- **超时:** 1800s
- **配置:** `C:\Users\fanci\projects\folo-push\config\folo-push-config.json`
- **状态:** `C:\Users\fanci\projects\folo-push\state\folo-push-state.json`
- **说明:** folo 晨报，优先补收昨夜晚落盘内容，再补今晨内容
- **通知:** 飞书(feishuTarget) → Telegram(telegramTarget) 兜底
- **纳入维护时间:** 2026-04-27

### 9. folo-instant-check
- **ID:** 1bd3c10a-b7f7-4d1b-8ed2-6abd5ad17adf
- **模型:** zai/glm-5.1
- **类型:** isolated / agentTurn (thinking: adaptive)
- **超时:** 900s
- **配置:** 同上
- **状态:** 同上
- **说明:** 即时检测高优先级内容，分数 ≥80 才推送，每天 4 次
- **通知:** 同上
- **纳入维护时间:** 2026-04-27

### 10. folo-daily-digest
- **ID:** 5b685768-924b-415f-b962-ef4a3ffb312f
- **模型:** zai/glm-5.1
- **类型:** isolated / agentTurn (thinking: adaptive)
- **超时:** 1800s
- **配置:** 同上
- **状态:** 同上
- **说明:** folo 晚报，汇总当天值得看的内容，必要时补收录
- **通知:** 同上
- **纳入维护时间:** 2026-04-27

### 11. folo-weekly-digest
- **ID:** 2a0fb379-df8f-41ac-975e-1d1f2cf9dfd1
- **模型:** zai/glm-5.1
- **类型:** isolated / agentTurn (thinking: adaptive)
- **超时:** 2400s
- **配置:** 同上
- **状态:** 同上
- **说明:** folo 周报，总结最近 7 天并补强未及时纳入的高价值条目
- **通知:** 同上
- **纳入维护时间:** 2026-04-27

### 7. OpenClaw Release 追踪
- **ID:** 875e85e4-b1fd-470e-bec2-96f1715f8312
- **状态:** ⏸️ 已禁用（系统仍存在）
- **最后修改:** 2026-04-16
- **说明:** disabled 状态，2026-04-27 确认系统仍存在（之前误标为已删除）

**巡检日志 - 2026-04-25 01:06 UTC (2026-04-25 09:06 CST)**
- ⚠️ 每日凌晨记日记：consecutiveErrors=1，超时 300s（与昨日相同，未恶化）
- ✅ 其余 5 个启用任务正常，consecutiveErrors=0
- ℹ️ Release 追踪：disabled ~29 天，未达 30 天阈值
- 📊 任务匹配度：7/7 一致，无新增/删除
- 🔇 无变更，未通知

**巡检日志 - 2026-04-23 20:23 UTC (2026-04-24 04:23 CST)**
- ❌ 每日凌晨记日记：consecutiveErrors=1，超时 300s（之前连续3次错误后恢复过，现在再次出错）
- ✅ 其余 5 个启用任务正常，consecutiveErrors=0
- ℹ️ Release 追踪：disabled 28 天，未达 30 天阈值
- 📊 任务匹配度：list 7 个(6启用+1禁用) = 档案一致，无新增/删除
- 🔔 已通知飞书

**巡检日志 - 2026-04-21 22:41 UTC (2026-04-22 06:41 CST)**
- ❌ 每日凌晨记日记：consecutiveErrors=3，持续超时（上次=2，恶化中）
- ⚠️ OpenClaw Release 追踪：已从系统彻底删除（档案更新为已删除）
- ✅ 其余 4 个启用任务正常，consecutiveErrors=0
- 📊 任务匹配度：list 6 个 + 档案归档 1 个已删除 = 一致
- 🔔 已通知飞书

**巡检日志 - 2026-04-20 03:02 UTC (2026-04-21 11:02 CST)**
- ❌ 每日凌晨记日记：agent 无响应，consecutiveErrors=2
- ⚠️ OpenClaw Release 追踪：档案记录为"已删除"，实际系统状态为"disabled"
- ✅ 其余 5 个启用任务正常，consecutiveErrors=0
- 📊 任务匹配度：7/7 一致，无新增/删除任务
- 🔔 已通知飞书

**巡检日志 - 2026-04-19 20:23 UTC (2026-04-20 04:23 CST)**
- ❌ folo-health-check:超时错误,consecutiveErrors=1
- ❌ 每日凌晨记日记:agent 无响应,consecutiveErrors=1
- ✅ 其余 4 个任务正常,consecutiveErrors=0
- 📊 任务匹配度:6/6 一致 + 1 已删除归档,无新增/删除任务
- 🔔 已通知飞书

**巡检日志 - 2026-04-26 20:30 UTC (2026-04-27 04:30 CST)**
- ✅ 6/6 启用任务全部正常，consecutiveErrors=0
- 🎉 每日凌晨记日记已恢复（上次 consecutiveErrors=1，现归零）
- ⚠️ Release 追踪：档案误标为"已删除"，实际系统仍存在（disabled），已修正
- ℹ️ 任务6名称更新为"OpenClaw 配置与凭证备份"
- 📊 任务匹配度：7/7 一致，无新增/删除
- 🔔 已通知飞书

## 维护规则

1. 任何新增/修改/删除任务前,先向 THE ONE 确认
2. 变更后同步更新本档案
3. 每周巡检一次运行状态,异常主动报告
4. 已禁用任务超过 30 天无计划时提醒清理

---

**巡检日志 - 2026-04-12 04:23 UTC**
- ✅ 所有启用任务状态正常,consecutiveErrors=0
- ⚠️ 发现:Release 追踪任务已禁用 32.7 天,超过 30 天阈值(已超期2.7天)
- 📊 任务状态:6/7 任务正常运行,1个任务需要处理
- 🔧 任务匹配度:100%(7/7 任务一致)
- 🔔 通知状态:多个任务存在通知失败(lastDeliveryStatus="not-delivered")