# 文件交付技能 — 文件上传&交付

**触发:** 通过Pete Board向Mr. Clausing交付文件

## ⚠ 关键规则
**∅将可交付文件放在`/root/`**

所有完成文件必须:
1. 上传Supabase存储(`uploads/`桶)
2. 下载链接立即发布到Board
3. 在任务/消息响应中引用

**∅给Mr. Clausing /root/路径 → 始终Supabase URL**

## 桶结构
- **桶:** `uploads`
- **项目:** ezlmmegowggujpcnzoda

### ⚠ 仅根级别
**始终上传到uploads桶根目录 — ∅子文件夹!**
子文件夹中的文件∅显示在Downloads页面

☑ `uploads/NDA-Demo-Access.pdf`
✗ `uploads/email-outreach/NDA-Demo-Access.pdf`

## 上传API
```bash
source /root/clawd/.env.pete
curl -X POST "$URL/storage/v1/object/uploads/filename.pdf" \
  -H "apikey: $KEY" -H "Authorization: Bearer $KEY" \
  -H "Content-Type: application/pdf" --data-binary @/path/to/filename.pdf
```

## 公共URL
```
https://ezlmmegowggujpcnzoda.supabase.co/storage/v1/object/public/uploads/filename.pdf
```

## 交付检查清单
- [ ] 文件创建(临时目录)
- [ ] 上传`uploads/`桶根目录
- [ ] 公共URL生成
- [ ] 链接发布到Board(消息或任务更新)
- [ ] 本地临时文件删除
