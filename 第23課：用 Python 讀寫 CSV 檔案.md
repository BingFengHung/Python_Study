# 第 23 課：用 Python 讀寫 CSV 檔案
## CSV 檔案介紹
CSV (Comma Separated Values) 稱為逗號分隔值檔案是一種簡單、通用的檔案格式，被廣泛的應用於應用程式 (資料庫、電子表格等) 資料的匯入與匯出以及異質系統之間的資料交換。

因為 CSV 是純文字檔案，不管是什麼作業系統和程式語言都可以處裡純文字，而且很多程式語言中都提供了對讀寫 CSV 檔案的支援，因此 CSV 格式在資料處裡和資料科學中被廣泛應用。

CSV 檔案有以下特點：

1. 純文字，使用某種字元集 (如 ASCII、UNICODE、GB2312) 等
2. 由一筆筆的紀錄組成 (典型的是每行一筆紀錄)
3. 每筆紀錄被分隔符號 (如逗號、分號、 Tab) 分隔為欄位 (列)
4. 每筆記物都有同樣的欄位序列。

CSV 檔案可以使用文字編輯器或類似於 Excel 這類工具開啟和編輯，當使用 Excel 打開 CSV 檔案時，甚至感受不到 CSV 與 Excel 檔案的區別。很多資料庫系統都支援將資料匯出到 CSV 檔案中，當然也支援從 CSV 檔案中讀取資料儲存到資料庫中。

## 將資料寫入 CSV 檔案
現在有五個學生三門課程的考試成績需要儲存到一個 CSV 檔案中，要達成此目的，可以使用 Python 標準函式庫中的 `csv` 模組，該模組的 `writer` 函數會返回一個 `csvwriter` 物件，透過該物件的 `writerow` 或 `writerows` 方法就可以將資料寫入到 CSV 檔案中，程式碼如下所示：

```py
import csv
import random

with open('scores.csv', 'w') as file:
    writer = csv.writer(file)
    writer.writerow(['姓名', '國文', '數學', '英文'])
    names = ['Joe', 'Tom', 'Jerry', 'Kate', 'Amy']
    for name in names:
        scores = [random.randrange(50, 101) for _ in range(3)]
        scores.insert(0, name)
        writer.writerow(scores)
```

生成的 CSV 檔案的內容。

```
姓名,國文,數學,英文
Joe,98,86,61
Tom,86,58,80
Jerry,95,73,70
Kate,83,97,55
Amy,61,54,87
```

需要說明的是上面的 `writer` 函數，除了傳入要寫入資料的檔案物件外，還可以使用 `dialect` 參數，他表示 CSV 檔案的方言，預設為 `excel`。除此之外，還可以使用 `delimiter`、`quotechar`、`quoting` 參數來指定分隔號 (預設為逗號)、包圍值得字元 (預設為雙引號) 以及包圍的方式。其中，包圍值的字元主要用於當欄位中有特殊符號時，透過加入包圍值的字元可以避免歧異性。

測試將上面的程式碼修改為下方程式碼：

```py
writer = csv.writer(file, delimiter='|', quoting=csv.QUOTE_ALL)
```

產生的 CSV 檔案的內容

```
"姓名"|"國文"|"數學"|"英文"
"Joe"|"88"|"64"|"65"
"Tom"|"76"|"93"|"79"
"Jerry"|"78"|"55"|"76"
"Kate"|"72"|"77"|"68"
"Amy"|"70"|"72"|"51"
```

## 從 CSV 檔案讀取資料
如果要讀取剛剛建立的 CSV 檔案，可以透過 `csv` 模組的 `reader` 函數可以建立出 `csvreader` 物件，該物件是一個迭代器，透過 `next` 函數或是 `for-in` 迴圈取到檔案的資料。

```py
import csv

with open('scores.csv', 'r') as file:
    reader = csv.reader(file, delimiter='|')
    for data_list in reader:
        print(reader.line_num, end='\t')
        for elem in data_list:
            print(elem, end='\t')
        print()
```

:fire: 上面的程式碼對 `csvreader` 物件做 `for` 迴圈時，每次會拿出一個串列物件，該串列物件包含了一行中所有的欄位。

## 總結
之後進行資料分析，可能會使用到 `pandas` 第三方函式庫，他是 Python 資料分析的神器之一。

`pandas` 裡面封裝了名為 `read_csv` 和 `to_csv` 的函數用來讀寫 csv 檔案，其中 `read_csv` 會將讀到的資料變成一個 `DataFrame` 物件，並且 `DataFrame` 就是 `pandas` 函式庫中最重要的類型，他封裝了一系列用於資料處裡的方法 (清洗、轉換、聚合等等)；而 `to_csv` 會將 `DataFrame` 物件中的資料寫到 csv 檔案，完成資料的持久化。
