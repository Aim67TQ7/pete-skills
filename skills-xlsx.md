# XLSX技能 — 专业电子表格创建&编辑

**触发:** 创建/编辑/分析Excel文件(.xlsx .xlsm .csv)

## 快速参考

| 任务 | 工具 |
|------|------|
| 数据分析/批量操作 | pandas |
| 公式/格式/样式 | openpyxl |
| 公式重算 | LibreOffice |

## 关键规则 CRITICAL

### 使用公式∅硬编码值
```python
# ❌ sheet['B10'] = df['Sales'].sum()
# ☑ sheet['B10'] = '=SUM(B2:B9)'
```

### 专业标准
**字体:** Arial或Times New Roman(全篇一致)

**颜色编码(财务模型):**
- 蓝(0,0,255)→硬编码输入值
- 黑(0,0,0)→所有公式/计算
- 绿(0,128,0)→同工作簿跨表链接
- 红(255,0,0)→外部文件链接
- 黄背景(255,255,0)→关键假设

**数字格式:**
- 年份→文本"2024" ∅"2,024"
- 货币→$#,##0 单位在标题("Revenue ($mm)")
- 零→显示"-" 格式`$#,##0;($#,##0);-`
- 百分比→0.0%
- 负数→括号(123) ∅-123

## 读取数据(pandas)
```python
import pandas as pd
df = pd.read_excel('file.xlsx')
all_sheets = pd.read_excel('file.xlsx', sheet_name=None)
df.to_excel('output.xlsx', index=False)
```

## 创建专业表格(openpyxl)
```python
from openpyxl import Workbook, load_workbook
from openpyxl.styles import Font, PatternFill, Alignment, Border, Side
from openpyxl.utils import get_column_letter

wb = Workbook(); sheet = wb.active; sheet.title = "Summary"

header_font = Font(name='Arial', size=12, bold=True)
input_font = Font(name='Arial', size=11, color='0000FF')
thin_border = Border(left=Side(style='thin'), right=Side(style='thin'),
    top=Side(style='thin'), bottom=Side(style='thin'))

headers = ['Description','Q1','Q2','Q3','Q4','Total']
for col, h in enumerate(headers, 1):
    cell = sheet.cell(row=1, column=col, value=h)
    cell.font = header_font
    cell.fill = PatternFill('solid', fgColor='D9E1F2')
    cell.alignment = Alignment(horizontal='center')
    cell.border = thin_border

sheet['B2'] = 100000; sheet['B2'].font = input_font  # 输入→蓝
sheet['F2'] = '=SUM(B2:E2)'  # 公式→黑

for row in range(2,10):
    for col in range(2,7):
        sheet.cell(row=row, column=col).number_format = '$#,##0'
wb.save('report.xlsx')
```

## 编辑现有文件
```python
wb = load_workbook('existing.xlsx')
sheet = wb.active
sheet['A1'] = 'New Value'
sheet.insert_rows(2); sheet.delete_cols(3)
wb.save('modified.xlsx')
```

## 公式重算(必须)
```bash
libreoffice --headless --calc -o output.xlsx --convert-to xlsx output.xlsx
```

## 常用公式

| 用途 | 公式 |
|------|------|
| 求和 | `=SUM(B2:B10)` |
| 平均 | `=AVERAGE(B2:B10)` |
| 条件 | `=IF(A1>100,"High","Low")` |
| 查找 | `=VLOOKUP(A1,Sheet2!A:B,2,FALSE)` |
| 同比 | `=(B2-A2)/A2` |
| 累计 | `=SUM($B$2:B2)` |

## 图表
```python
from openpyxl.chart import BarChart, Reference
chart = BarChart(); chart.title = "Revenue"
data = Reference(sheet, min_col=2, max_col=5, min_row=1, max_row=5)
chart.add_data(data, titles_from_data=True)
sheet.add_chart(chart, "H2"); wb.save('with_chart.xlsx')
```

## 工作流
1. 选择工具 → 2. 创建/加载工作簿 → 3. 数据+公式+格式
4. 保存 → 5. 重算公式 → 6. 上传Supabase`uploads/` → 7. 发布链接 → 8. 删除临时文件
