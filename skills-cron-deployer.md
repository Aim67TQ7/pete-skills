# Cron部署技能 — 自主Cron管理

**触发:** 创建定时任务/自动化脚本/∅调用Pete的循环作业

## 核心原则
Pete创建并部署cron → Pete∅参与cron执行
☑ Pete写bash脚本→部署cron→脚本独立运行
✗ Cron每5分钟调用Pete检查工作

## 脚本位置
```
/root/clawd/automation/
├── scripts/      ← 可执行脚本
├── logs/         ← 输出日志
└── crontab.d/    ← Cron定义(参考)
```

## 创建自动化任务

### 步骤1: 写脚本
```bash
cat > /root/clawd/automation/scripts/my-task.sh << 'SCRIPT'
#!/bin/bash
source /root/clawd/.env.pete
# 逻辑(∅AI调用除非用Gemini Flash)
# 可选→日志到Supabase
curl -X POST "$URL/rest/v1/automation_logs" ...
SCRIPT
chmod +x /root/clawd/automation/scripts/my-task.sh
```

### 步骤2: 测试
```bash
/root/clawd/automation/scripts/my-task.sh
```

### 步骤3: 部署cron
```bash
(crontab -l 2>/dev/null; echo "0 * * * * /root/clawd/automation/scripts/my-task.sh >> /root/clawd/automation/logs/my-task.log 2>&1") | crontab -
```

### 步骤4: 文档化
```bash
cat > /root/clawd/automation/crontab.d/my-task.md << 'DOC'
Schedule: Every hour | Script: path | Purpose: desc
DOC
```

## Cron语法参考

| 计划 | 表达式 |
|------|--------|
| 每5分 | `*/5 * * * *` |
| 每小时 | `0 * * * *` |
| 每日6am | `0 6 * * *` |
| 每日6am+6pm | `0 6,18 * * *` |
| 每周一9am | `0 9 * * 1` |
| 工作日8am | `0 8 * * 1-5` |

## 管理
```bash
crontab -l                              # 查看
crontab -l | grep -v "my-task" | crontab -  # 删除
```

## 告警Pete(需要时)
脚本→创建inbox任务→触发gatekeeper→唤醒Pete
```bash
curl -X POST "$URL/rest/v1/tasks" ... \
  -d '{"title":"Alert: X failed","status":"inbox","priority":"high"}'
```

## Cron中的AI
- **使用:** Gemini 2.0 Flash(廉价快速)
- **∅使用:** Opus/Sonnet/GPT-4
- 详见`lightweight-ai-crons.md`

## 规则
1. ∅Pete在cron执行中 — 纯bash/python
2. 脚本自包含 — ∅依赖Pete在线
3. 记录一切 — 文件和/或Supabase
4. 通过任务创建告警 — inbox任务
5. 文档化每个cron — 在crontab.d/
6. 仅Gemini Flash — 如需AI
