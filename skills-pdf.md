# PDF技能 — 专业PDF创建&处理

**触发:** 创建专业PDF | 读取/合并/拆分/填充PDF文件

## 快速参考

| 任务 | 工具 |
|------|------|
| 创建专业PDF | reportlab |
| 合并/拆分/旋转/加密 | pypdf |
| 提取文本(含布局) | pdfplumber |
| 提取表格 | pdfplumber+pandas |
| HTML→PDF | weasyprint |
| OCR扫描PDF | pytesseract+pdf2image |

## 创建专业PDF(reportlab)

### 基础设置
```python
from reportlab.lib.pagesizes import letter, A4
from reportlab.lib.units import inch
from reportlab.lib.styles import getSampleStyleSheet, ParagraphStyle
from reportlab.lib.colors import HexColor
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer, Table, TableStyle, Image, PageBreak
from reportlab.lib.enums import TA_CENTER, TA_LEFT, TA_RIGHT, TA_JUSTIFY

doc = SimpleDocTemplate("report.pdf", pagesize=letter,
    rightMargin=0.75*inch, leftMargin=0.75*inch,
    topMargin=0.75*inch, bottomMargin=0.75*inch)
story = []
styles = getSampleStyleSheet()
```

### 自定义样式
```python
title_style = ParagraphStyle('CustomTitle', parent=styles['Heading1'],
    fontSize=24, textColor=HexColor('#1a1a2e'), spaceAfter=30,
    alignment=TA_CENTER, fontName='Helvetica-Bold')

heading_style = ParagraphStyle('CustomHeading', parent=styles['Heading2'],
    fontSize=14, textColor=HexColor('#16213e'), spaceBefore=20,
    spaceAfter=10, fontName='Helvetica-Bold')

body_style = ParagraphStyle('CustomBody', parent=styles['Normal'],
    fontSize=11, textColor=HexColor('#333333'), spaceBefore=6,
    spaceAfter=6, alignment=TA_JUSTIFY, fontName='Helvetica', leading=14)
```

### 构建内容
```python
story.append(Paragraph("Title", title_style))
story.append(Spacer(1, 0.25*inch))
story.append(Paragraph("Section", heading_style))
story.append(Paragraph("Body text...", body_style))
```

### 专业表格
```python
from reportlab.lib import colors
data = [['Metric','Q3','Q4','Change'], ['Revenue','$1.2M','$1.5M','+25%']]
table = Table(data, colWidths=[2*inch, 1.25*inch, 1.25*inch, 1*inch])
table.setStyle(TableStyle([
    ('BACKGROUND', (0,0), (-1,0), HexColor('#1a1a2e')),
    ('TEXTCOLOR', (0,0), (-1,0), colors.white),
    ('FONTNAME', (0,0), (-1,0), 'Helvetica-Bold'),
    ('FONTSIZE', (0,0), (-1,-1), 10),
    ('ALIGN', (1,1), (-1,-1), 'CENTER'),
    ('ROWBACKGROUNDS', (0,1), (-1,-1), [colors.white, HexColor('#f8f9fa')]),
    ('GRID', (0,0), (-1,-1), 0.5, HexColor('#dee2e6')),
]))
story.append(table)
```

### 图片/分页/构建
```python
story.append(Image('chart.png', width=5*inch, height=3*inch))
story.append(PageBreak())
doc.build(story)
```

## HTML→PDF(weasyprint)
```python
from weasyprint import HTML, CSS
HTML(string=html_content).write_pdf('report.pdf')
HTML('report.html').write_pdf('report.pdf', stylesheets=[CSS('style.css')])
```

## Excel→PDF
```python
import pandas as pd
from reportlab.platypus import SimpleDocTemplate, Table, TableStyle
from reportlab.lib.pagesizes import landscape, letter

def excel_to_pdf(excel_path, pdf_path, title="Report"):
    df = pd.read_excel(excel_path)
    doc = SimpleDocTemplate(pdf_path, pagesize=landscape(letter))
    data = [df.columns.tolist()] + df.values.tolist()
    table = Table(data)
    # 应用样式...
    doc.build([table])
```

## 读取&提取(pypdf)
```python
from pypdf import PdfReader, PdfWriter
reader = PdfReader("doc.pdf")
text = "".join(p.extract_text() for p in reader.pages)
```

## 合并PDF
```python
writer = PdfWriter()
for f in ["a.pdf","b.pdf"]:
    for p in PdfReader(f).pages: writer.add_page(p)
writer.write(open("merged.pdf","wb"))
```

## 拆分PDF
```python
for i, page in enumerate(PdfReader("input.pdf").pages):
    w = PdfWriter(); w.add_page(page)
    w.write(open(f"page_{i+1}.pdf","wb"))
```

## 表格提取(pdfplumber)
```python
import pdfplumber, pandas as pd
with pdfplumber.open("doc.pdf") as pdf:
    for page in pdf.pages:
        for t in page.extract_tables():
            df = pd.DataFrame(t[1:], columns=t[0])
```

## 密码保护
```python
writer = PdfWriter()
for p in PdfReader("input.pdf").pages: writer.add_page(p)
writer.encrypt("user_pw", "owner_pw")
writer.write(open("encrypted.pdf","wb"))
```

## ⚠ 下标/上标
**∅使用Unicode下标字符**(₀₁₂₃)→黑框渲染
```python
# 使用XML标记
Paragraph("H<sub>2</sub>O", styles['Normal'])
Paragraph("x<super>2</super>", styles['Normal'])
```

## 工作流
1. 选择工具 → 2. 创建/处理PDF → 3. 保存临时位置
4. 上传Supabase`uploads/`桶 → 5. 发布链接到Board → 6. 删除临时文件
