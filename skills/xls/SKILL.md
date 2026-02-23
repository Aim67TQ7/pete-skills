---
name: xls
description: "电子表格创建/转换/读取/分析 — LibreOffice"
metadata:
  openclaw:
    emoji: "📊"
    requires:
      bins: ["libreoffice"]
---

# XLS/电子表格技能

## 能力
- 格式转换(xlsx/xls/csv/ods/pdf)
- 读取→CSV分析
- 从数据创建表格
- CSV→格式化Excel

## 表格→PDF
```bash
libreoffice --headless --convert-to pdf --outdir /tmp /path/to/data.xlsx
```

## 表格→CSV(读取/分析)
```bash
libreoffice --headless --convert-to csv --outdir /tmp /path/to/data.xlsx
```

## CSV→Excel
```bash
libreoffice --headless --convert-to xlsx --outdir /tmp /path/to/data.csv
```

## 从零创建
```bash
cat > /tmp/report.csv << 'CSVEOF'
Name,Value,Status
Item A,100,Active
CSVEOF
libreoffice --headless --convert-to xlsx --outdir /tmp /tmp/report.csv
```

## Supabase下载→处理→上传
```bash
KEY=$(grep -o 'eyJ[^"]*' skills/project-manager/supabase.sh | head -1)
URL="https://ezlmmegowggujpcnzoda.supabase.co"
curl -s -H "apikey: $KEY" -H "Authorization: Bearer $KEY" "$URL/storage/v1/object/reference-docs/uploads/FILE" -o /tmp/FILE
libreoffice --headless --convert-to pdf --outdir /tmp /tmp/FILE
bash skills/project-manager/supabase.sh upload PROJECT_ID /tmp/FILE.pdf application/pdf
```

## 支持格式
xlsx, xls, csv, ods, tsv, html(表格)
