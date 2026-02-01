---
name: xlsx
description: Comprehensive spreadsheet creation, editing, and analysis with support for formulas, formatting, data analysis, and visualization. Use when working with spreadsheets (.xlsx, .xlsm, .csv, .tsv) for creating, reading, analyzing, or modifying Excel files.
license: Apache-2.0
source: anthropics/skills
---

# XLSX Processing Guide

## Overview

A guide for creating, editing, and analyzing Excel files using Python libraries.

## CRITICAL: Use Formulas, Not Hardcoded Values

**Always use Excel formulas instead of calculating values in Python and hardcoding them.** This ensures the spreadsheet remains dynamic and updateable.

### ❌ WRONG - Hardcoding Calculated Values
```python
# Bad: Calculating in Python and hardcoding result
total = df['Sales'].sum()
sheet['B10'] = total  # Hardcodes 5000

# Bad: Computing growth rate in Python
growth = (df.iloc[-1]['Revenue'] - df.iloc[0]['Revenue']) / df.iloc[0]['Revenue']
sheet['C5'] = growth  # Hardcodes 0.15
```

### ✅ CORRECT - Using Excel Formulas
```python
# Good: Let Excel calculate the sum
sheet['B10'] = '=SUM(B2:B9)'

# Good: Growth rate as Excel formula
sheet['C5'] = '=(C4-C2)/C2'

# Good: Average using Excel function
sheet['D20'] = '=AVERAGE(D2:D19)'
```

## Reading and Analyzing Data

### Data Analysis with pandas
```python
import pandas as pd

# Read Excel
df = pd.read_excel('file.xlsx')  # Default: first sheet
all_sheets = pd.read_excel('file.xlsx', sheet_name=None)  # All sheets as dict

# Analyze
df.head()       # Preview data
df.info()       # Column info
df.describe()   # Statistics

# Write Excel
df.to_excel('output.xlsx', index=False)
```

## Creating New Excel Files

### Using openpyxl for Formulas and Formatting
```python
from openpyxl import Workbook
from openpyxl.styles import Font, PatternFill, Alignment

wb = Workbook()
sheet = wb.active

# Add data
sheet['A1'] = 'Hello'
sheet['B1'] = 'World'
sheet.append(['Row', 'of', 'data'])

# Add formula
sheet['B2'] = '=SUM(A1:A10)'

# Formatting
sheet['A1'].font = Font(bold=True, color='FF0000')
sheet['A1'].fill = PatternFill('solid', start_color='FFFF00')
sheet['A1'].alignment = Alignment(horizontal='center')

# Column width
sheet.column_dimensions['A'].width = 20

wb.save('output.xlsx')
```

## Editing Existing Excel Files

```python
from openpyxl import load_workbook

# Load existing file
wb = load_workbook('existing.xlsx')
sheet = wb.active  # or wb['SheetName'] for specific sheet

# Working with multiple sheets
for sheet_name in wb.sheetnames:
    sheet = wb[sheet_name]
    print(f"Sheet: {sheet_name}")

# Modify cells
sheet['A1'] = 'New Value'
sheet.insert_rows(2)   # Insert row at position 2
sheet.delete_cols(3)   # Delete column 3

# Add new sheet
new_sheet = wb.create_sheet('NewSheet')
new_sheet['A1'] = 'Data'

wb.save('modified.xlsx')
```

## Financial Model Standards

### Color Coding Conventions
- **Blue text (RGB: 0,0,255)**: Hardcoded inputs, scenario values
- **Black text (RGB: 0,0,0)**: ALL formulas and calculations
- **Green text (RGB: 0,128,0)**: Links from other worksheets
- **Red text (RGB: 255,0,0)**: External links to other files
- **Yellow background (RGB: 255,255,0)**: Key assumptions needing attention

### Number Formatting Standards
```python
from openpyxl.styles import numbers

# Currency
sheet['A1'].number_format = '$#,##0'

# Percentages
sheet['B1'].number_format = '0.0%'

# Multiples (valuation)
sheet['C1'].number_format = '0.0x'

# Negative numbers with parentheses
sheet['D1'].number_format = '$#,##0;($#,##0);-'
```

## Common Formulas

```python
# Financial Formulas
sheet['A1'] = '=NPV(0.1, B1:B10)'              # Net Present Value
sheet['A2'] = '=IRR(B1:B10)'                    # Internal Rate of Return
sheet['A3'] = '=PMT(0.05/12, 360, -100000)'     # Loan Payment

# Lookup Formulas
sheet['A4'] = '=VLOOKUP(A1, B:C, 2, FALSE)'    # Vertical Lookup
sheet['A5'] = '=INDEX(B:B, MATCH(A1, A:A, 0))' # Index Match

# Conditional Formulas
sheet['A6'] = '=IF(B1>100, "High", "Low")'     # IF Statement
sheet['A7'] = '=SUMIF(A:A, ">0", B:B)'         # Conditional Sum
sheet['A8'] = '=COUNTIF(A:A, "Yes")'           # Conditional Count

# Date Formulas
sheet['A9'] = '=TODAY()'                        # Current Date
sheet['A10'] = '=EOMONTH(A1, 0)'               # End of Month
```

## Best Practices

### Library Selection
- **pandas**: Best for data analysis, bulk operations, simple data export
- **openpyxl**: Best for complex formatting, formulas, Excel-specific features

### Working with openpyxl
- Cell indices are 1-based (row=1, column=1 refers to cell A1)
- Use `data_only=True` to read calculated values: `load_workbook('file.xlsx', data_only=True)`
- **Warning**: If opened with `data_only=True` and saved, formulas are replaced with values
- For large files: Use `read_only=True` for reading or `write_only=True` for writing

### Working with pandas
- Specify data types: `pd.read_excel('file.xlsx', dtype={'id': str})`
- For large files, read specific columns: `pd.read_excel('file.xlsx', usecols=['A', 'C', 'E'])`
- Handle dates: `pd.read_excel('file.xlsx', parse_dates=['date_column'])`

## Quick Reference

| Task | Best Tool | Code |
|------|-----------|------|
| Read data | pandas | `pd.read_excel('file.xlsx')` |
| Write simple data | pandas | `df.to_excel('output.xlsx')` |
| Complex formatting | openpyxl | `Font()`, `PatternFill()` |
| Add formulas | openpyxl | `sheet['A1'] = '=SUM(B:B)'` |
| Merge cells | openpyxl | `sheet.merge_cells('A1:C1')` |
| Charts | openpyxl | `BarChart()`, `LineChart()` |

## Required Dependencies

```bash
pip install openpyxl pandas xlrd
```
