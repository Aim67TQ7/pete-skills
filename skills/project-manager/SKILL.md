---
name: project-manager
description: "自主项目编排器 — 管理项目全生命周期via Supabase+Dashboard"
metadata:
  openclaw:
    emoji: "📋"
---

# 项目管理器技能

## Supabase访问
**URL:** https://ezlmmegowggujpcnzoda.supabase.co

### Helper脚本
```bash
bash skills/project-manager/supabase.sh list-queued    # 列出排队项目
bash skills/project-manager/supabase.sh list-all       # 列出所有
bash skills/project-manager/supabase.sh get <id>       # 获取特定
bash skills/project-manager/supabase.sh update <id> '{"status":"active","pete_commentary":"Working"}'
bash skills/project-manager/supabase.sh log-tokens <model> <provider> <in> <out> <cost> <caller>
bash skills/project-manager/supabase.sh token-spend    # 今日花费
bash skills/project-manager/dashboard.sh rebuild       # 重建Dashboard
```

## 工作流

### 每次心跳:
1. `list-queued`检查新项目
2. 每个排队项目→分析→确定代理/技能→状态"active"→开始工作
3. 检查活跃项目更新
4. 记录token使用

### 项目状态
- **queued** → 新, 等待认领
- **active** → 已确认, 规划中
- **in_progress** → 工作进行中
- **complete** → 可交付就绪

### 可用代理(本VPS)
- **MAGNUS**(8401)→ Bunting设备专家
- **JIMMY**(systemd)→ Supabase数据分析
- **NATE**(systemd)→ n8n工作流构建

### 完成项目(含可交付物)
```bash
# 一步完成(上传+完成)
bash skills/project-manager/supabase.sh complete-project <id> /tmp/deliverable.md "text/markdown"
```
上传→`completed-tasks`桶 → 更新项目`status:complete`+`output_url`

**支持格式:** .md .txt .html .json .jsx .js .csv .pdf .png .jpg

### Dashboard控制
```bash
cd /root/pete-dashboard-src && npm run build && cp -r dist/* /opt/pete-dashboard/
```

## 文档转换
```bash
bash skills/project-manager/convert.sh /path/to/file.docx pdf
```

## 关键规则
- ∅排队项目超30分钟无确认
- 始终更新pete_commentary
- 始终记录token使用
- 做不到→说明→∅假装
- 每个完成项目必须有上传的可交付物
