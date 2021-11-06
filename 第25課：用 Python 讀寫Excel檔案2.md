# 第 25 課：用 Python 讀寫 Excel 檔案 2

本篇將使用另一個 `openpyxl` 對 Excel 檔案進行操作

安裝指令如下：

```bash
pip install openpyxl
```

`openpyxl` 的優點在於，當開啟一個 Excel 檔案後，既可以對她進行讀取操作，又可以對他進行寫入操作，而且在操作的便利性上面是優於 `xlwt` 和 `xlrd` 的。此外，如果要進行樣式編輯與公式計算，使用 `openpyxl` 也比前一篇所說的方式更為簡單，並且 `openpyxl` 還包含樞紐分析和插入圖表等操作，功能強大，不過 `openpyxl` 並不知源操作 Office 2007 以前版本的 Excel 檔案。

## 讀取 Excel 檔案
例如在當前資料夾下有一個名為股票資料.xlsx 的 Excel 檔案，如需讀取並顯示該檔案內容，可以透過以下程式碼：

```py
import datetime

import openpyxl

# 載入一個工作簿 -> Workbook
wb = openpyxl.load_workbook('股票資料.xlsx')
# 印出每個工作表的名稱
print(wb.sheetnames)
# 取得工作表 -> Worksheet
sheet = wb.worksheets[0]
# 取得單元格的範圍
print(sheet.dimensions)
# 取得列數與行數
print(sheet.max_row, sheet.max_column)

# 取得指定單元格的值
print(sheet.cell(3, 3).value)
print(sheet['C3'].value)
print(sheet['G255'].value)

# 取得多個單元格 (嵌套元組)
print(sheet['A2:C5'])

# 讀取所有單元格的資料
for row_ch in range(2, sheet.max_row + 1):
    for col_ch in 'ABCDEFG':
        value = sheet[f'{col_ch}{row_ch}'].value
        if type(value) == datetime.datetime:
            print(value.strftime('%Y年%m月%d日'), end='\t')
        elif type(value) == int:
            print(f'{value:<10d}', end='\t')
        elif type(value) == float:
            print(f'{value:.4f}', end='\t')
        else:
            print(value, end='\t')
    print()
```
:fire: `openpyxl` 取得指定的單元格有兩種方式，一種是透過 `cell` 方法，要注意，此方法的列索引與行索引都是從 `1` 開始的，這是為了按照習慣使用 Excel 的人；另一種是透過索引運算，透過指定單元格的座標，例如 `C3`、`G255`，也可以取得對應的單元格，再透過單元格物件的 `value` 屬性，就能夠取得單元格的值。

從上面程式碼可以看到，也可以透過類似 `sheet['A2:C5']` 或 `sheet['A2':'C5']` 這樣的切片操作取得多個單元格，該操作將返回嵌套的元組，相當於取得到多行多列。

## 寫入 Excel 檔案
下面使用 `openpyxl` 進行寫入 Excel 操作。

```py
import random

import openpyxl

# 第一步：建立工作簿 (Workbook)
wb = openpyxl.Workbook()

# 第二步：加入工作表 (Worksheet)
sheet = wb.active
sheet.title = 'Scores'

titles = ('Name', 'Chinese', 'Math', 'English')
for col_index, title in enumerate(titles):
    sheet.cell(1, col_index + 1, title)

names = ('Joe', 'Tom', 'Jerry', 'Kate', 'Amy')
for row_index, name in enumerate(names):
    sheet.cell(row_index + 2, 1, name)
    for col_index in range(2, 5):
        sheet.cell(row_index + 2, col_index, random.randrange(50, 101))

# 第四步：儲存工作簿
wb.save('Scores.xlsx')
```

## 調整樣式和公式計算
在使用 `openpyxl` 操作 Excel 時，如果要調整單元格的樣式，可以直接透過單元格物件 (`Cell` 物件) 的屬性進行操作。

單元格物件的屬性包含字體 (`font`)、對齊 (`alignment`)、邊框 (`border`)等，具體操作可以參考 `openpyxl 的官方文件`，如果需要公式計算，具體操作如下所示：

```py
import openpyxl
from openpyxl.styles import Font, Alignment, Border, Side

# 對齊方式
alignment = Alignment(horizontal='center', vertical='center')
# 邊框線條
side = Side(color='ff7f50', style='mediumDashed')

wb = openpyxl.load_workbook('scores.xlsx')
sheet = wb.worksheets[0]

# 調整行高和列寬
sheet.row_dimensions[1].height = 30
sheet.column_dimensions['E'].width = 120

sheet['E1'] = '平均分數'
# 字體設定
sheet.cell(1, 5).font = Font(size=18, bold=True, color='ff1493', name='標楷體')
# 設定對齊方式
sheet.cell(1, 5).alignment = alignment
# 設定單元格邊框
sheet.cell(1, 5).border = Border(left=side, top=side, right=side, bottom=side)
for i in range(2, 7):
    # 公式計算每個學生的平均分數
    sheet[f'E{i}'] = f'=average(B{i}:D{i})'
    sheet.cell(i, 5).font = Font(size=12, color='4169e1', italic=True)
    sheet.cell(i, 5).alignment = alignment

wb.save('scores.xlsx')
```

## 產生統計圖表
透過 `openpyxl` 函式庫，可以直接向 Excel 中插入統計圖表，作法與 Excel 中插入圖表大致一樣，可以建立指定類型的圖表物件，然後透過該物件的屬性對圖表進行設定。

最重要的是為圖表綁定資料，即橫軸代表甚麼，縱軸代表甚麼，具體資料是多少。最後，可以將圖表物件加入到表單中，程式碼如下所示：

```py
from openpyxl import Workbook
from openpyxl.chart import BarChart, Reference

wb = Workbook(write_only=True)
sheet = wb.create_sheet()

rows = [
    ('categories', 'A group', 'B group'),
    ('Phone', 40, 30),
    ('Tablet', 50, 60),
    ('Notebook', 80, 70),
    ('Others', 20, 10),
]
# 向表單中加入列
for row in rows:
    sheet.append(row)

# 建立圖表物件
chart = BarChart()
chart.type = 'col'
chart.style = 10
# 設定圖表的標題
chart.title = '銷售統計圖'
# 設定圖表橫軸的標題
chart.y_axis.title = '數量'
# 設定圖表縱軸的標題
chart.x_axis.title = 'Categires'
# 設定資料的範圍
data = Reference(sheet, min_col=2, min_row=1, max_row=5, max_col=3)
# 設定類別的範圍
cats = Reference(sheet, min_col=1, min_row=2, max_row=5)
# 給圖表加入資料
chart.add_data(data, titles_from_data=True)
# 給圖表設定類別
chart.set_categories(cats)
chart.shape = 4
# 將圖表家道表單指定的單元格中
sheet.add_chart(chart, 'A10')

wb.save('demo.xlsx')
```
