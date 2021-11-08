# 第 27 課：用 Python 操作 PDF 檔案

PDF 是 Portable Document Format 的縮寫，通常使用 `.pdf` 作為副檔名。在開發過程中，最容易遇到的就是從 PDF 中讀取文件內容以及使用已有的內容產生 PDF 檔案這兩項任務。

## 从PDF中提取文本

## 從 PDF 中擷取檔案內容
使用名為 `PyPDF2` 的第三方函式庫來讀取 PDF 檔案。

```bash
pip install PyPDF2
```

:fire: `PyPDF2` 無法從 PDF 檔案中擷取圖像、圖表或是其他媒體，但是他可以擷取文字內容，並返回為 Python 字串。

```py
import PyPDF2

reader = PyPDF2.PdfFileReader('test.pdf')
page = reader.getPage(0)
print(page.extractText())
```
當然，`PyPDF2` 並不是甚麼樣的 PDF 檔案都能讀出文字出來。

要從 PDF 檔案中提取文字也可以直接使用第三方的命令提示工具，做法如下：

```bash
pip install pdfminer.six
pdf2text.py test.pdf
```

## 旋轉和疊加頁面

上面的程式碼透過建立 `PdfFileReader` 物件的方式讀取 PDF 檔案，該物件的 `getPage` 方法可以取得 PDF 檔案的指定頁並得到一個 `PageObject` 物件，透過 `PageObject` 物件的 `rotateClockwise` 和 `rotateCounterClockWise` 方法可以實現頁面的順時針與逆時針方向旋轉，透過 `PageObject` 物件的 `addBlankPage` 方法可以加入一個新的空白頁，程式碼如下所示：

```py
import PyPDF2

from PyPDF2.pdf import PageObject

# 建立一個讀 pdf 檔案的 Reader 物件 
reader = PyPDF2.PdfFileReader('resources/XGBoost.pdf')
# 建立一個寫 PDF 檔案的 Writer 物件
writer = PyPDF2.PdfFileWriter()
# 對 PDF 檔案所有頁面進行循環走訪
for page_num in range(reader.numPages):
    # 取得指定頁碼的 Page 物件
    current_page = reader.getPage(page_num)  # type: PageObject
    if page_num % 2 == 0:
        # 奇數頁順時針旋轉 90 度
        current_page.rotateClockwise(90)
    else:
        # 偶數頁逆時針旋轉 90 度
        current_page.rotateCounterClockwise(90)
    writer.addPage(current_page)
# 最後加入一個空白頁面並旋轉 90 度
page = writer.addBlankPage()  # type: PageObject
page.rotateClockwise(90)
# 透過 Writer 物件的 write 方法將 PDF 寫入檔案
with open('resources/XGBoost-modified.pdf', 'wb') as file:
    writer.write(file)
```

## 加密 PDF 檔案 
使用 `PyPDF2` 中的 `PdfFileWrite` 物件可以為 PDF 檔案可以為 PDF 檔案加密，如果需要給一系列 PDF 檔案設定統一的訪問 Token，使用 Python 程式來處理就會非常方便。

```py
import PyPDF2

reader = PyPDF2.PdfFileReader('resources/XGBoost.pdf')
writer = PyPDF2.PdfFileWriter()
for page_num in range(reader.numPages):
    writer.addPage(reader.getPage(page_num))
# 透過 encrypt 方法加密 PDF 檔案，方法的參數就是設定的密碼
writer.encrypt('foobared')
with open('resources/XGBoost-encrypted.pdf', 'wb') as file:
    writer.write(file)
```

## 批量添加浮水印

上面提到的 `PageObject` 物件還有一個名為 `mergePage` 的方法，可將兩個 PDF 頁面進行疊加，透過這個操作，可以很容易實現給 PDF 檔案加入浮水印的功能。

例如，要給上面的檔案加入一個浮水印，先準備好一個提供浮水頁頁面的 PDF 檔案，然後將包含浮水的的 `PageObject` 讀取出來，然後再循環走訪該檔案的每個頁面，取得到 `PageObject` 物件，然後透過 `mergePage` 方法實現浮水印和原始頁面的合併，程式碼如下所示：

```py
import PyPDF2

from PyPDF2.pdf import PageObject

reader1 = PyPDF2.PdfFileReader('resources/XGBoost.pdf')
reader2 = PyPDF2.PdfFileReader('resources/watermark.pdf')
writer = PyPDF2.PdfFileWriter()
# 取得浮水印頁面
watermark_page = reader2.getPage(0)
for page_num in range(reader1.numPages):
    current_page = reader1.getPage(page_num)  # type: PageObject
    current_page.mergePage(watermark_page)

    # 將原始頁面和浮水印頁面進行合併
    writer.addPage(current_page)

# 將 PDF 寫入檔案
with open('resources/XGBoost-watermarked.pdf', 'wb') as file:
    writer.write(file)
```

## 建立 PDF 檔案
建立 PDF 檔案需要第三方函數庫 `reportlab` 的支援

```bash
pip install reportlab
```

```py
from reportlab.lib.pagesizes import A4
from reportlab.pdfbase import pdfmetrics
from reportlab.pdfbase.ttfonts import TTFont
from reportlab.pdfgen import canvas

pdf_canvas = canvas.Canvas('resources/demo.pdf', pagesize=A4)
width, height = A4

# 繪圖
image = canvas.ImageReader('resources/guido.jpg')
pdf_canvas.drawImage(image, 20, height - 395, 250, 375)

# 顯示當前頁面
pdf_canvas.showPage()

# 註冊字體檔案
pdfmetrics.registerFont(TTFont('Font1', 'resources/fonts/Vera.ttf'))
pdfmetrics.registerFont(TTFont('Font2', 'resources/fonts/abcd.ttf'))

# 寫字
pdf_canvas.setFont('Font2', 40)
pdf_canvas.setFillColorRGB(0.9, 0.5, 0.3, 1)
pdf_canvas.drawString(width // 2 - 120, height // 2, '你好，世界！')
pdf_canvas.setFont('Font1', 40)
pdf_canvas.setFillColorRGB(0, 1, 0, 0.5)
pdf_canvas.rotate(18)
pdf_canvas.drawString(250, 250, 'hello, world!')

# 儲存
pdf_canvas.save()
```


上密的程式碼不理解也沒關係，等需要時，再好好研究一下 `reportlab` 的[官方文件](https://www.reportlab.com/docs/reportlab-userguide.pdf)就可以了。
