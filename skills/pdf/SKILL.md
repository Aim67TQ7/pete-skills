---
name: pdf
description: "PDF创建/转换/读取/操作 — LibreOffice"
metadata:
  openclaw:
    emoji: "📄"
    requires:
      bins: ["libreoffice"]
---

# PDF技能

## 能力
- 任意文档→PDF(docx/xlsx/pptx/odt/html/txt/csv)
- 提取PDF文本
- 合并多PDF
- 从零创建PDF

## 转换→PDF
```bash
libreoffice --headless --convert-to pdf --outdir /tmp /path/to/input.docx
```

## 提取文本
```bash
pdftotext /path/to/file.pdf /path/to/output.txt
```

## Supabase下载→转换→上传
```bash
KEY=$(grep -o 'eyJ[^"]*' skills/project-manager/supabase.sh | head -1)
URL="https://ezlmmegowggujpcnzoda.supabase.co"
curl -s -H "apikey: $KEY" -H "Authorization: Bearer $KEY" "$URL/storage/v1/object/reference-docs/uploads/FILE" -o /tmp/FILE
libreoffice --headless --convert-to pdf --outdir /tmp /tmp/FILE
bash skills/project-manager/supabase.sh upload PROJECT_ID /tmp/FILE.pdf application/pdf
```

## 支持输入格式
docx, doc, xlsx, xls, pptx, ppt, odt, ods, odp, html, txt, csv, rtf
