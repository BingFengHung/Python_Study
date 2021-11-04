# 第 24 課：用 Python 讀寫 Excel 檔案

## Excel 介紹
Excel 是微軟為使用 Windows 和 macOS 作業系統開發的一款電子表格軟體。Excel 憑藉直觀的介面、出色的計算功能和圖表工具，再加上成功的市場行銷，一直都是最為流行的個人電腦資料處裡軟體。

當然 Excel 也有很多競爭商品，例如 Google Sheets、LibreOffice Calc、Numbers 等，這些競爭商品基本上也能夠兼容 Excel，至少能夠讀寫較新版本的 Excel 檔案。掌握用 Python 程式操作 Excel 檔案，可以讓日常辦公室自動化的工作更加輕鬆，並且在很多商業項目中，匯入與匯出 Excel 檔案都是很常見的功能。

Excel是Microsoft（微软）为使用Windows和macOS操作系统开发的一款电子表格软件。Excel凭借其直观的界面、出色的计算功能和图表工具，再加上成功的市场营销，一直以来都是最为流行的个人计算机数据处理软件。当然，Excel也有很多竞品，例如Google Sheets、LibreOffice Calc、Numbers等，这些竞品基本上也能够兼容Excel，至少能够读写较新版本的Excel文件，当然这些不是我们讨论的重点。掌握用Python程序操作Excel文件，可以让日常办公自动化的工作更加轻松愉快，而且在很多商业项目中，导入导出Excel文件都是特别常见的功能。

Python 操作 Excel 需要第三方函式庫的支援，如果要兼容 Excel 2007 以前的版本，也就是 `xls` 格式的 Excel 檔案，可以使用第三方函式庫 `xlrd` 和 `xlwt`，前者用於讀取 Excel 檔案，後者用於寫入 Excel 檔案。如果使用比較新版的 Excel，即操作 `xlsx` 格式的 Excel 檔案，也可以使用 `openpyxl` 函數庫，當然這個函式庫不僅可以操作 Excel，還可以操作其他基於 Office Open XML 的電子表格檔案。

下面以 `xlwt` 和 `xlrd` 來說明如何讀寫檔案

```bash
pip install xlwt xlrd 
```

## 使用 xlwt 和 xlrd 
### 讀取 Excel 檔案
假設當前資料夾下有一個名為"股票資料.xls"的 Excel 檔案，如果想讀取並顯示該檔案的內容，可以透過以下程式碼來完成。

```py
import xlrd

# 使用 xlrd 模組的 open_workbook 函數開啟指定 Excel 檔案並取得 Book 物件 (工作簿)
wb = xlrd.open_workbook('股票資料.xlsx')

# 透過 Book 物件的 sheet_names 方法可以取得所有表單名稱
sheetname = wb.sheet_names()[0]

# 透過指定的表單名稱取得 Sheet 物件 (工作表)
sheet = wb.sheet_by_name(sheetname)

# 透過 Sheet 物件的 nrows 和 ncols 屬性取得表單的行數與列數
print(sheet.nrows, sheet.ncols)
for row in range(sheet.nrows):
    for col in range(sheet.ncols):
        # 透過 Sheet 物件的 cell 方法取得指定 Cell 物件 (單元格)
        # 透過 Cell 物件的 value 屬性取得單元格中的值
        value = sheet.cell(row, col).value

        # 對除了第一行以外的其他行進行資料格式處理 
        if row > 0:
            # 第 1 列的 xldate 類型顯轉成元組在格式化為"年月日"的格式
            if col == 0:
                # xldate_as_tuple 函數的第二個參數只有 0 和 1 兩個取值
                # 其中 0 表示 1900-01-01 為基準的日期，1 代表以 1904-01-01 為基準的日期
                value = xlrd.xldate_as_tuple(value, 0)
                value = f'{value[0]}年{value[1]:>02d}月{value[2]:>02d}日'
            # 其他列的 number 類型處裡成小數點後保留兩位有效數字的浮點數
            else:
                value = f'{value:.2f}'
        print(value, end='\t')
    print()
# 取得最後一個單元格的資料類型
# 0 - 空值，1 - 字串，2 - 數字，3 - 日期，4 - 布林，5 - 錯誤
last_cell_type = sheet.cell_type(sheet.nrows - 1, sheet.ncols - 1)
print(last_cell_type)
# 取得第一行的值 (列表)
print(sheet.row_values(0))
#　取得指定行指定列範圍的資料 (列表)
# 第一個參數代表行索引，第二個和第三個參數代表列的開始 (包含) 和結束 (不含) 索引
print(sheet.row_slice(3, 0, 5))
```
### 寫入 Excel 檔案
寫入 Excel 檔案可透過 `xlwt` 模組的 `Workbook` 類別建立工作簿物件，透過工作簿物件的 `add_sheet` 方法可以加入工作表，透過工作表物件的 `write` 方法可以向指定單元格中寫入資料，最後透過工作簿物件的 `save` 方法將工作簿寫入到止地的檔案或是記憶體中。

下面的程式碼實作將 5 個學生 3 門課程的考試成績寫入 Excel 檔案的操作。

```py
import random

import xlwt

student_names = ['Joe', 'Tom', 'Jerry', 'Kate', 'Amy']
scores = [[random.randint(40, 100) for _ in range(3)] for _ in range(5)]

# 建立工作簿物件 (Workbook)
wb = xlwt.Workbook()

# 建立工作表物件 (Worksheet) 
sheet = wb.add_sheet('class 1')

# 加入表頭資料
titles = ('Name', 'Chinese', 'Math', 'English')
for index, title in enumerate(titles):
    sheet.write(0, index, title)

# 將學生姓名和考試成績寫入單元格
for row in range(len(scores)):
    sheet.write(row + 1, 0, student_names[row])
    for col in range(len(scores[row])):
        sheet.write(row + 1, col + 1, scores[row][col])

# 儲存 Excel 工作簿
wb.save('成績表.xlsx')
```
## 調整單元格樣式

在寫 Excel 檔案時，還可以幫單元格設定樣式，主要包含字體 (Font)、對齊方式 (Alignment)、邊框 (Border) 和背景 (Background) 的設定，`xlwt` 對這幾項設定都封裝了對應的類別來支援。

要設定單元格樣式首先需要先建立一個 `XFStyle` 物件，再透過該物件的屬性對字體、對齊方式、邊框等進行設定。

例如，上面的例子，如希望對表頭單元格的背景色修改為黃色，可以按照下列程式碼所示：

```py
header_style = xlwt.XFStyle()
pattern = xlwt.Pattern()
pattern.pattern = xlwt.Pattern.SOLID_PATTERN

# 0 - 黑色、1 - 白色、2 - 紅色、3 - 綠色、4 - 藍色、5 - 黃色、6 - 粉紅色、7 - 青色
pattern.pattern_fore_colour = 5

header_style.pattern = pattern

titles = ('Name', 'Chinese', 'Math', 'English')
for index, title in enumerate(titles):
    sheet.write(0, index, title, header_style)
```

如果要設定表頭為指定的字體，可以使用 `Font` 類別，並加入如下所示的程式碼。

```py
font = xlwt.Font()
# 字體名稱
font.name = '標楷體'
# 字體大小 (20 為基本單位，18 為 18px))
font.height = 20 * 18

# 是否使用粗體
font.bold = True
# 是否使用斜體
font.italic = False
# 字體顏色
font.colour_index = 1
header_style.font = font
```

如果希望表頭垂直置中對齊，可使用下面的程式碼進行設定

```py
align = xlwt.Alignment()

# 垂直方向的對齊方式
align.vert = xlwt.Alignment.VERT_CENTER

# 水平方向的對齊方式
align.horz = xlwt.Alignment.HORZ_CENTER
header_style.alignment = align
```

如果要給表頭加上黃色的虛線邊框，可以使用下面的程式碼進行設定

```py
borders = xlwt.Borders()
props = (
    ('top', 'top_colour'), ('right', 'right_colour'),
    ('bottom', 'bottom_colour'), ('left', 'left_colour')
)

# 透過迴圈對四個方向的邊框樣式及顏色進行設定
for position, color in props:
    setattr(borders, position, xlwt.Borders.DASHED)
    setattr(borders, color, 5)

header_style.borders = borders
```

如果要調整單元格的寬度 (列寬) 和表頭的高度 (行高)，可以按照下面的程式碼進行操作

```py
# 設定行高為 40px
sheet.row(0).set_style(xlwt.easyxf(f'font:height {20 * 40}'))

titles = ('name', 'Chinese', 'Math', 'English')
for index, title in enumerate(titles):
    # 設定列寬為 200px 
    sheet.col(index).width = 20 * 200
    # 設定單元格的資料和樣式
    sheet.write(0, index, title, header_style)
```

## 公式計算

如果要統計前面股票資料的全年收盤價 (Close 欄位) 的平均值以及全年交易量 (Volumn 欄位) 的總和，使用 Excel 的公式計算即可。

先使用 `xlrd` 讀取 Excel 資料夾，然後透過一個名為 `xlutils` 的第三方函式庫提供的 `copy` 函數將讀取到的 Excel 檔案轉換成 `Workbook` 物件進行寫入操作，再呼叫 `write` 方法時，可以將一個 `Formula` 物件寫入單元格中。

安裝 `xlutils` 第三方函式庫。

```Bash
pip install xlutils 
```

實現公式計算的程式碼如下所示

```py
import xlrd
import xlwt
from xlutils.copy import copy

wb_for_read = xlrd.open_workbook('股票資料.xlsx')
sheet1 = wb_for_read.sheet_by_index(0)
nrows, ncols = sheet1.nrows, sheet1.ncols
wb_for_write = copy(wb_for_read)
sheet2 = wb_for_write.get_sheet(0)
sheet2.write(nrows, 4, xlwt.Formula(f'average(E2:E{nrows})'))
sheet2.write(nrows, 6, xlwt.Formula(f'sum(G2:G{nrows})'))
wb_for_write.save('股票資料.xlsx')
```
