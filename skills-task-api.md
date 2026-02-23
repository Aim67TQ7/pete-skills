# 任务API技能 — 任务&消息操作

**触发:** 创建/更新/查询Pete Board上的任务和消息

## 基础配置
- **Supabase:** ezlmmegowggujpcnzoda
- **REST:** `https://ezlmmegowggujpcnzoda.supabase.co/rest/v1`
- **凭证:** `source /root/clawd/.env.pete`

## 任务表 TASKS

### 模式
| 列 | 类型 | 说明 |
|----|------|------|
| id | uuid | PK |
| title | text | 任务标题 |
| description | text | 详情 |
| status | text | `inbox`/`in_progress`/`done`/`archived` |
| priority | text | `low`/`normal`/`high` |
| updates | jsonb | 进度更新数组 |
| created_at | timestamp | 自动 |

### 获取inbox
```bash
curl -X GET "$URL/tasks?status=eq.inbox" -H "apikey: $KEY" -H "Authorization: Bearer $KEY"
```

### 创建任务
```bash
curl -X POST "$URL/tasks" -H "apikey: $KEY" -H "Authorization: Bearer $KEY" \
  -H "Content-Type: application/json" -H "Prefer: return=representation" \
  -d '{"title":"...","status":"inbox","priority":"normal"}'
```

### 更新状态
```bash
curl -X PATCH "$URL/tasks?id=eq.<uuid>" -H "apikey: $KEY" -H "Authorization: Bearer $KEY" \
  -H "Content-Type: application/json" -d '{"status":"in_progress"}'
```

### 添加进度
```bash
curl -X PATCH "$URL/tasks?id=eq.<uuid>" ... \
  -d '{"updates":[{"text":"Started...","time":"ISO8601"}]}'
```

## 消息表 MESSAGES

### 模式
| 列 | 类型 | 说明 |
|----|------|------|
| id | uuid | PK |
| content | text | 消息体 |
| sender | text | `user`/`pete` |
| read | boolean | 已读 |
| created_at | timestamp | 自动 |

### 获取用户消息
```bash
curl -X GET "$URL/messages?sender=eq.user&order=created_at.desc" -H "apikey: $KEY" -H "Authorization: Bearer $KEY"
```

### Pete发送消息
```bash
curl -X POST "$URL/messages" -H "apikey: $KEY" -H "Authorization: Bearer $KEY" \
  -H "Content-Type: application/json" -H "Prefer: return=representation" \
  -d '{"content":"Done! Link: [url]","sender":"pete"}'
```

### 标记已读
```bash
curl -X PATCH "$URL/messages?id=eq.<uuid>" ... -d '{"read":true}'
```

## 任务工作流
1. 检查inbox→ `tasks?status=eq.inbox`
2. 认领→ 状态更新`in_progress`
3. 工作→ 按需添加进度更新
4. 完成→ 状态`done` + 发布最终消息
