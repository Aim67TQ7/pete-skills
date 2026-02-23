---
name: doc
description: "文档创建/转换/读取/编辑 — LibreOffice+pandoc"
metadata:
  openclaw:
    emoji: "📝"
    requires:
      bins: ["libreoffice", "pandoc"]
---

# DOC/Word文档技能

## 能力
- 格式转换(docx/doc/pdf/html/txt/odt/rtf/md)
- 读取文档→纯文本
- 从markdown/文本创建文档

## Word→PDF
```bash
libreoffice --headless --convert-to pdf --outdir /tmp /path/to/doc.docx
```

## 读取文档内容
```bash
pandoc /path/to/doc.docx -t plain
```

## Markdown→Word
```bash
pandoc /path/to/input.md -o /tmp/output.docx
```

## HTML→Word
```bash
pandoc /path/to/page.html -o /tmp/output.docx
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
docx, doc, odt, rtf, txt, md, html, epub
