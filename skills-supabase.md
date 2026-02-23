# Supabase技能 — 基础设施参考

**触发:** 需要项目详情/表模式/桶信息

## 项目详情
- **ID:** ezlmmegowggujpcnzoda
- **API:** `https://ezlmmegowggujpcnzoda.supabase.co`
- **REST:** `.../rest/v1`
- **Storage:** `.../storage/v1`

## 表 TABLES

| 表 | 用途 |
|----|------|
| `tasks` | 看板任务管理 |
| `messages` | 用户↔Pete聊天 |
| `pete_secrets` | API密钥/凭证 |
| `pete_config` | 设置/偏好 |
| `parking_lot` | 思绪/语音备忘 |
| `news_digests` | 新闻摘要输出 |
| `automation_logs` | Cron执行日志 |

## 存储桶 BUCKETS

| 桶 | 用途 |
|----|------|
| `uploads` | 交付文件(Downloads页面) |

⚠ **仅根级别** — 子文件夹∅显示在Downloads UI

## 认证 AUTH
```bash
-H "apikey: $SUPABASE_SERVICE_KEY"
-H "Authorization: Bearer $SUPABASE_SERVICE_KEY"
# 变更操作追加:
-H "Content-Type: application/json"
# 返回记录:
-H "Prefer: return=representation"
```

## 连接服务

| 服务 | URL |
|------|-----|
| Pete Board | petes-board.netlify.app |
| GitHub | github.com/Aim67TQ7/petes-board |

## 本地路径

| 路径 | 内容 |
|------|------|
| `/root/clawd/` | 主目录 |
| `/root/clawd/.env.pete` | 本地凭证(仅引导) |
| `/root/clawd/scripts/` | 工具脚本 |
| `/root/clawd/skills/` | 技能文档 |
| `/root/clawd/automation/` | Cron脚本+日志 |
