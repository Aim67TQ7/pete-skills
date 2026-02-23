# DOCX技能 — 专业Word文档创建&编辑

**触发:** 创建新Word文档或编辑现有.docx文件

## 快速参考

| 任务 | 方法 |
|------|------|
| 读取/分析内容 | python-docx或pandoc |
| 创建新文档 | python-docx |
| 复杂模板 | python-docx+styles |
| 格式转换 | pandoc或LibreOffice |

## 创建专业文档(python-docx)

### 基础设置
```python
from docx import Document
from docx.shared import Inches, Pt, RGBColor
from docx.enum.text import WD_ALIGN_PARAGRAPH
from docx.enum.style import WD_STYLE_TYPE

doc = Document()
section = doc.sections[0]
section.page_width = Inches(8.5); section.page_height = Inches(11)
section.left_margin = section.right_margin = Inches(1)
section.top_margin = section.bottom_margin = Inches(1)
```

### 自定义样式
```python
title_style = doc.styles.add_style('CustomTitle', WD_STYLE_TYPE.PARAGRAPH)
title_style.font.name = 'Arial'; title_style.font.size = Pt(24)
title_style.font.bold = True; title_style.font.color.rgb = RGBColor(26,26,46)
title_style.paragraph_format.alignment = WD_ALIGN_PARAGRAPH.CENTER

heading_style = doc.styles.add_style('CustomHeading', WD_STYLE_TYPE.PARAGRAPH)
heading_style.font.name = 'Arial'; heading_style.font.size = Pt(14)
heading_style.font.bold = True; heading_style.font.color.rgb = RGBColor(22,33,62)

body_style = doc.styles.add_style('CustomBody', WD_STYLE_TYPE.PARAGRAPH)
body_style.font.name = 'Arial'; body_style.font.size = Pt(11)
body_style.paragraph_format.line_spacing = 1.15
```

### 构建内容
```python
doc.add_paragraph('Report Title', style='CustomTitle')
doc.add_paragraph('Executive Summary', style='CustomHeading')
doc.add_paragraph('Body text...', style='CustomBody')
```

### 专业表格
```python
from docx.oxml.ns import nsdecls
from docx.oxml import parse_xml

table = doc.add_table(rows=5, cols=4)
# 标题行→白字深蓝背景
for idx, h in enumerate(['Metric','Q3','Q4','Change']):
    cell = table.rows[0].cells[idx]; cell.text = h
    run = cell.paragraphs[0].runs[0]
    run.font.bold = True; run.font.color.rgb = RGBColor(255,255,255)
    shading = parse_xml(f'<w:shd {nsdecls("w")} w:fill="1a1a2e"/>')
    cell._tc.get_or_add_tcPr().append(shading)
```

### 列表/图片/分页
```python
doc.add_paragraph('Item text', style='List Bullet')
doc.add_paragraph('Step text', style='List Number')
doc.add_picture('chart.png', width=Inches(5.5))
doc.add_page_break()
```

### 页眉/页脚
```python
header = doc.sections[0].header
header.paragraphs[0].text = "Confidential"
```

### 保存
```python
doc.save('report.docx')
```

## 读取现有文档
```python
doc = Document('existing.docx')
for para in doc.paragraphs: print(para.text)
for table in doc.tables:
    for row in table.rows:
        for cell in row.cells: print(cell.text)
```

## 转PDF
```bash
libreoffice --headless --convert-to pdf report.docx --outdir ./
```

## 从Markdown转换
```bash
pandoc input.md -o output.docx
```

## 安装
```bash
pip install python-docx --break-system-packages
```

## 工作流
1. python-docx创建 → 2. 应用样式 → 3. 内容(文本/表格/图片)
4. 保存 → 5. 转PDF(如需) → 6. 上传Supabase`uploads/` → 7. 发布链接 → 8. 删除临时文件
