# 第 26 課：用 Python 操作 Word 和 PowerPoint
在日常生活中，簡單重複的動作其實可以完全交給 Python 程式，例如根據樣板檔案 (模板檔案) 批量生成多個 Word 檔案或是 PowerPoint 檔案。

在 Python 中，可以使用名為 `python-docx` 的第三方函式庫來操作 Word，也可以使用 `python-pptx` 的第三方函式庫來產生 PowerPoint。

## 鄵做 Word 檔案

先透過下面的指令安裝`python-docx` 第三方函式庫


```bash
pip install python-docx
```

按照

按照[官方文件](https://python-docx.readthedocs.io/en/latest/)的介紹，可以使用如下所示的程式碼來生成一個簡單的 Word 檔案。

```py
from docx import Document
from docx.shared import Cm, Pt

from docx.document import Document as Doc

# 建立代表 Word 檔案的 Doc 物件
document = Document()  # type: Doc
# 加入大標題
document.add_heading('快快樂樂學 Python', 0)
# 加入段落
p = document.add_paragraph('Python 是一門非常流行的程式語言，他')
run = p.add_run('簡單')
run.bold = True
run.font.size = Pt(18)
p.add_run('而且')
run = p.add_run('優雅')
run.font.size = Pt(18)
run.underline = True
p.add_run('。')

# 加入第一級標題 
document.add_heading('Heading, level 1', level=1)
# 加入帶樣式的段落
document.add_paragraph('Intense quote', style='Intense Quote')
# 加入無順序列表
document.add_paragraph(
    'first item in unordered list', style='List Bullet'
)
document.add_paragraph(
    'second item in ordered list', style='List Bullet'
)
# 加入有順序列表
document.add_paragraph(
    'first item in ordered list', style='List Number'
)
document.add_paragraph(
    'second item in ordered list', style='List Number'
)

# 加入圖片 (需注意路徑和圖片必須要存在)
document.add_picture('resources/guido.jpg', width=Cm(5.2))

# 加入分節符號
document.add_section()

records = (
    ('Joe', 'boy', '1995-5-5'),
    ('Amy', 'giry', '1992-2-2')
)
# 加入表格
table = document.add_table(rows=1, cols=3)
table.style = 'Dark List'
hdr_cells = table.rows[0].cells
hdr_cells[0].text = 'Name'
hdr_cells[1].text = 'Gender'
hdr_cells[2].text = 'Birth'

# 為表格加入列
for name, sex, birthday in records:
    row_cells = table.add_row().cells
    row_cells[0].text = name
    row_cells[1].text = sex
    row_cells[2].text = birthday

# 加入分頁符號
document.add_page_break()

# 儲存檔案
document.save('demo.docx')
```

:fire: 上面程式碼中第 7 行中的註解 `# type: Doc` 是為了在 PyCharm 中取得程式碼補齊提示，因為如果不清楚物件具體的資料類型，Python 無法在後面程式碼給出 `Doc` 物件的程式碼補齊提示。


對於一個已經存在的 Word 檔案，可透過下面的程式碼去走訪他所有的段落並取得對應的內容。

```py
from docx import Document
from docx.document import Document as Doc

doc = Document('resources/申請表.docx')  # type: Doc
for no, p in enumerate(doc.paragraphs):
    print(no, p.text)
```

讀取到的內容如下所示：

```
0 
1 申請表
2 
3 兹證明 王小名，身分證號碼： X123456789，於 2021 年 8 月 8 號完成全馬 42 公里馬拉松
4 成績 2小時 18 分鐘
5 特此證明！
6 
7 
8 活動單位: XXX　
9    			2021 年 8 月 8 日
```

說到這邊，已經可將上面的文件，做成一個模板文件，將姓名、身分證號碼、成績等資訊使用佔位符代替，這樣透過佔位符的替換，就可以根據實際需要寫入對應的資訊，這樣就可以批量產生 Word 檔案。

接下來讀取該文件，將佔位符替換成實際的資訊，就可以升成一個新的 Word 檔案，如下程式碼所示：

```py
from docx import Document
from docx.document import Document as Doc

# 將實際資料用字典類型儲存在串列中
employees = [
    {
        'name': 'Joe',
        'id': '123123123',
        'date': '2021年8月8日',
        'score': '1 小時 18 分',
    },
    {
        'name': 'Tom',
        'id': '123456789',
        'date': '2021年8月8日',
        'score': '1 小時 53 分',
    },
    {
        'name': 'Jerry',
        'id': '987654321',
        'date': '2021年8月8日',
        'score': '1 小時 44 分',
    },
]
# 對串列進行迴圈，批量產生檔案
for emp_dict in employees:
    # 讀取申請表檔案
    doc = Document('resources/申請表.docx')  # type: Doc
    # 迴圈走訪所有段落尋找佔位符
    for p in doc.paragraphs:
        if '{' not in p.text:
            continue
        # 不能直接修改段落內容，否則會遺失樣式
        # 因此需要對段落中的元素進行走訪並進行查找替換
        for run in p.runs:
            if '{' not in run.text:
                continue
            # 將佔位符換成實際內容
            start, end = run.text.find('{'), run.text.find('}')
            key, place_holder = run.text[start + 1:end], run.text[start:end + 1]
            run.text = run.text.replace(place_holder, emp_dict[key])
    # 每個人對應儲存成一個 Word 文件 
    doc.save(f'{emp_dict["name"]}申請表.docx')
```
## 產生 PowerPoint 

首先需要安裝名為 `python-pptx` 的第三方函式庫，指令如下所示：

```bash
pip install python-pptx
```

用 Python 操作 PowerPoint 的內容

```py
from pptx import Presentation

# 建立投影片物件 
pres = Presentation()

# 選擇母版加入一頁
title_slide_layout = pres.slide_layouts[0]
slide = pres.slides.add_slide(title_slide_layout)

# 取得標題與副標題欄位 
title = slide.shapes.title
subtitle = slide.placeholders[1]
# 編輯標題與副標題
title.text = "Welcome to Python"
subtitle.text = "Life is short, I use Python"

# 選擇母版加入一頁
bullet_slide_layout = pres.slide_layouts[1]
slide = pres.slides.add_slide(bullet_slide_layout)
# 取得頁面上所有形狀
shapes = slide.shapes
# 取得標題和主體 
title_shape = shapes.title
body_shape = shapes.placeholders[1]
# 編輯標題
title_shape.text = 'Introduction'
# 編輯主體內容
tf = body_shape.text_frame
tf.text = 'History of Python'
# 加入第一級段落
p = tf.add_paragraph()
p.text = 'X\'max 1989'
p.level = 1
# 加入第二級段落
p = tf.add_paragraph()
p.text = 'Guido began to write interpreter for Python.'
p.level = 2

# 存儲投影片
pres.save('test.pptx')
```
