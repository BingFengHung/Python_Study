# 第 20 課：Python 標準函式庫初探
Python 標準函式庫與第三方函式庫很豐富，日常開發中的很多任務都可以透過這些標準函式庫或第三方函式庫直接解決。下面首先介紹 Python 標準函式庫中常用的模組。

## base64 - Base64 編碼解碼模組

**Base64** 是一種基於 64 個可印出的字元來表示二進制資料的方法，
<img src="https://render.githubusercontent.com/render/math?math=log _{2}64=6"> 
所以 Base64 以 6 個位元 (二進制，可表示為 0 或 1) 為一個單位 (字元)，每個單位對應一個可列印字串，對於 3 個位元組 (24 位元) 的二進制資料，可以將其處理對應於 4 個 Base64 單元，即 3 個位元組可由 4 個可列印的字串表示。

Base64 編碼可以用來當作電子郵件的傳輸編碼，也可用於其他需要將二進制資料轉成字串的情境包含 `A-Z`、`a-z`、`0-9`，這邊一共為 62 個字元，另外兩個可以列印的符號通常是 `+` 和 `/`、`=` 用於在 Base64 編碼最後面進行補位。

Python 標準函式庫中的 `base64` 模組提供了 `b64encode` 和 `b64decode` 兩個函數，專門用來實現 Base64 的編碼語解碼，程式碼如下所示。

```py
>>> import base64
>>> 
>>> content = 'Man is distinguished, not only by his reason, but by this singular passion from other animals, which is a lust of the mind, that by a perseverance of delight in the continued and indefatigable generation of knowledge, exceeds the short vehemence of any carnal pleasure.'

>>> base64.b64encode(content.encode())
b'TWFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5IGhpcyByZWFzb24sIGJ1dCBieSB0aGlzIHNpbmd1bGFyIHBhc3Npb24gZnJvbSBvdGhlciBhbmltYWxzLCB3aGljaCBpcyBhIGx1c3Qgb2YgdGhlIG1pbmQsIHRoYXQgYnkgYSBwZXJzZXZlcmFuY2Ugb2YgZGVsaWdodCBpbiB0aGUgY29udGludWVkIGFuZCBpbmRlZmF0aWdhYmxlIGdlbmVyYXRpb24gb2Yga25vd2xlZGdlLCBleGNlZWRzIHRoZSBzaG9ydCB2ZWhlbWVuY2Ugb2YgYW55IGNhcm5hbCBwbGVhc3VyZS4='

>>> content = b'TWFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5IGhpcyByZWFzb24sIGJ1dCBieSB0aGlzIHNpbmd1bGFyIHBhc3Npb24gZnJvbSBvdGhlciBhbmltYWxzLCB3aGljaCBpcyBhIGx1c3Qgb2YgdGhlIG1pbmQsIHRoYXQgYnkgYSBwZXJzZXZlcmFuY2Ugb2YgZGVsaWdodCBpbiB0aGUgY29udGludWVkIGFuZCBpbmRlZmF0aWdhYmxlIGdlbmVyYXRpb24gb2Yga25vd2xlZGdlLCBleGNlZWRzIHRoZSBzaG9ydCB2ZWhlbWVuY2Ugb2YgYW55IGNhcm5hbCBwbGVhc3VyZS4='

>>> base64.b64decode(content).decode()
'Man is distinguished, not only by his reason, but by this singular passion from other animals, which is a lust of the mind, that by a perseverance of delight in the continued and indefatigable generation of knowledge, exceeds the short vehemence of any carnal pleasure.'
```

## collections - 容器資料類型模組
`collections` 模組提供了許多非常好用的資料結構，主要包含：
- `namedtuple`：命令元組，他是一個類別工廠，接受類型的名稱和屬性列表來建立一個類別。
- `deque`：兩端佇列，是列表的替代實作。Python 中的串列底層是基於資料來實作的，而 `deque` 底層是雙向串列，因此當需要在頭尾加入和刪除元素時，`deque` 會有更好的性能，漸進時間複雜度為 O(1)。
- `Counter`：`dict` 的子類別，鍵是元素，值是元素的計數，他的 `most_common()` 方法可以幫助獲取出現頻率最高的元素。
- `OrderedDict`：`dict` 的子類別，紀錄了鍵值對插入的順序，看起來既有字典的行為，也有鏈表的行為。
- `defaultdict`：類似於字典類型，但是可以透過預設的工廠函數來取得鍵對應的默認值，相比字典中的 `setdefault()` 方法，這種做法更加有效率。

下面是在 Python 互動式環境中使用 `namedtuple` 建立撲克牌類別的範例。

```py
>>> from collections import namedtuple
>>>
>>> Card = namedtuple('Card', ('suite', 'face'))
>>> card1 = Card('红桃', 5)
>>> card2 = Card('梅花', 9)
>>> card1
Card(suite='红桃', face=5)
>>> card2
Card(suite='梅花', face=9)
>>> print(f'{card1.suite}{card1.face}')
红桃 5
>>> print(f'{card2.suite}{card2.face}')
梅花 9
```

下面是使用 `Counter` 類別統計串列中出現次數最多的三個元素的範例。

```py
from collections import Counter

words = [
    'look', 'into', 'my', 'eyes', 'look', 'into', 'my', 'eyes',
    'the', 'eyes', 'the', 'eyes', 'the', 'eyes', 'not', 'around',
    'the', 'eyes', "don't", 'look', 'around', 'the', 'eyes',
    'look', 'into', 'my', 'eyes', "you're", 'under'
]

counter = Counter(words)
# 印出 words 串列中出現頻率最高的三個元素及其出現次數
for elem, count in counter.most_common(3):
    print(elem, count)
```
## hashlib - 哈希函數模組
哈希函數又稱作哈希算法或是散列函數，是一種為已有的資料建立 "數字指紋" (哈希摘要) 的方法。

哈希函數把資料壓縮成摘要，對於相同的輸入，哈希函數可以生成相同的摘要 (數字指紋)，需要注意的是這個過程為不可逆的 (不可透過摘要計算出輸入內容)。一個好的哈希函數能夠為不同的輸入產生不同的摘要，出現哈希衝突 (不同輸入計算出相同的摘要) 的機率極低。

Python 標準函式庫的 `hashlib` 模組提供了對哈希函數的封裝，透過使用 `md5`、`sha1`、`sha256` 等類別，可以輕鬆的產生 "數字指紋"。

例如，使用者註冊時，我們希望在資料庫中保存使用者的密碼，很明顯我們不能將使用者密碼直接儲存在資料庫中，這樣可能會導致使用者隱私外洩，所以在資料庫儲存使用者密碼時，通常會將密碼指紋保存，使用者登入時，透過哈希函數計算密碼的"指紋"在進行匹配來判斷使用者登入是否成功。

```py
import hashlib

# 計算字串 "123456" 的 MD5 摘要
print(hashlib.md5('123456'.encode()).hexdigest())

# 計算檔案 "Python-3.7.1.tar.xz" 的 MD5 摘要
hasher = hashlib.md5()
with open('Python-3.7.1.tar.xz', 'rb') as file:
    data = file.read(512)
    while data:
        hasher.update(data)
        data = file.read(512)
print(hasher.hexdigest())
```

> **說明**：很多網站在下載連結的旁邊都提供了哈希摘要，完成檔案下載後，可以計算該文件的哈希摘要並檢查她與網路上的哈希摘要是否一致 (指紋比對)。如果計算出來的與網站上面提供的不一致，有可能是下載出錯或是檔案在傳輸過程中被竄改，此時就不應該直接使用此檔案。

## heapq - 堆積排序模組
`heapq` 模組時做了堆積排序演算法，如果希望使用堆積排序，尤其是要解決 **TopK 問題** (從序列中找到 K 個最大或最小元素)，直接使用該模組即可，程式碼如下所示：

```py
import heapq

list1 = [34, 25, 12, 99, 87, 63, 58, 78, 88, 92]
# 找出列表中最大的三個元素
print(heapq.nlargest(3, list1))
# 找出列表中最小的三個元素
print(heapq.nsmallest(3, list1))

list2 = [
    {'name': 'IBM', 'shares': 100, 'price': 91.1},
    {'name': 'AAPL', 'shares': 50, 'price': 543.22},
    {'name': 'FB', 'shares': 200, 'price': 21.09},
    {'name': 'HPQ', 'shares': 35, 'price': 31.75},
    {'name': 'YHOO', 'shares': 45, 'price': 16.35},
    {'name': 'ACME', 'shares': 75, 'price': 115.65}
]

# 找出價格最高的三支股票
print(heapq.nlargest(3, list2, key=lambda x: x['price']))
# 找出持有數量最高的三支股票
print(heapq.nlargest(3, list2, key=lambda x: x['shares']))
```
## itertools - 迭代工具模組 
`itertools` 可以生成各式各樣的迭代，如下程式碼所示：

```py
import itertools

# 產生 ABCD 的全排列
for value in itertools.permutations('ABCD'):
    print(value)

# 產生 ABCDE 的五選三組合
for value in itertools.combinations('ABCDE', 3):
    print(value)

# 產生ABCD 和 123 的笛卡爾積
for value in itertools.product('ABCD', '123'):
    print(value)

# 產生 ABC 的無限循環序列
it = itertools.cycle(('A', 'B', 'C'))
print(next(it))
print(next(it))
print(next(it))
print(next(it))
```
## random - 隨機亂數和隨機抽樣模組

下面是常用函數的列表，
- `getrandbits(k)`： 返回具有 `k` 個隨機位元單位的整數
- `randrange(start, stop[, step])`： 從 `range(start, stop, step)` 返回一個隨機選擇的元素，但實際上並沒有建立一個 `range` 物件。
- `randint(a, b)`：返回隨機整數 `N` 滿足 `a <= N <= b`，相當於 `randrange(a, b+1)`。
- `choice(seq)`：從非空序列 `seq` 返回一個隨機元素。如果 `seq` 為空，則導致 `IndexError`
- `choices(population, weight=None, *, cum_weights=None, k=1)`：從 `population` 中選擇替換，返回大小為 `k` 的元素列表。如果 `population` 為空，則導致 `IndexError`。
- `shuffle(x[, random])`： 將序列 `x` 隨機打亂位置
- `sample(population, k)`： 返回從總體序列或是集合中選擇 `k` 個不重複元素構建的列表，用於無重複的隨機抽樣。
- `random()`：返回`[0.0, 1.0)`範圍內的下一個隨機浮點數
- `expovariate(lambd)`：指數分布。
- `gammavariate(alpha, beta)`：伽瑪分配。
- `gauss(mu, sigma)` / `normalvariate(mu, sigma)`： 正態分布
- `paretovariate(alpha)`：柏拉圖分布。 
- `weibullvariate(alpha, beta)`：威布爾分布

### os.path - 路徑操作相關模組

`os.path` 模組封裝了操作路徑的工具函數，如果城市中需要對檔案路徑做拼接、拆分、獲取以及獲取檔案的存在性和其他屬性，這個模組將會很有幫助

- `dirname(path)`： 返回路徑 `path` 的目錄名稱
- `exists(path)`：如果 `path` 指向一個已經存在的路徑或已經打開的檔案描述符，返回 `True`。
- `getatime(path)` / `getmtime(path)` / `getctime(path)`：返回 `path` 的最後訪問時間/最後修改時間/建立時間
- `getsize(path)`：返回`path`的大小，以位元為單位。如果該文件不存在或是不可訪問，就會拋出 `OSError` 異常
- `isfile(path)`： 如果 `path` 是檔案，救回傳 true
- `isdir(path)`：如果 `path` 是資料夾，就返回 true
- `join(path, *paths)`：去拼接一個或多個路徑部分，返回值是 `path` 和 `paths` 所有值的連接，每個非空部分後面都緊跟一個目錄分隔符 (`os.sep`)，除了最後一部分。這意味著如果最後一部分為空，
，則結果將以分隔符號結尾。如果參數中某個部分是絕對位置，則絕對路徑前的路徑都將會被丟棄，並從絕對路徑部分開始連接。
- `splitext(path)`：将路径`path`拆分为一对，即`(root, ext)`，使得`root + ext == path`，其中`ext`为空或以英文句点开头，且最多包含一个句点。

### uuid - UUID 生成模組
`uuid` 模組可以幫助我們產生全域唯一標示符 (Universal Unique Identity)，此模組提供了四個用於產生 UUID 的函數，分別是：

- `uuid1()`： 由 MAC 位址、當前時間戳記、隨機亂數產生，可以保證全球範圍內的唯一性。
- `uuid3(namespace, name)`：透過計算命名空間和名字的 MD5 哈希摘要 (指紋) 值得到，保證同一命名空間中不同名字的唯一性，和不同命名空間的唯一性，但同一命名空間的同一名字會產生相同的 UUID。
- `uuid4()`：由偽隨機亂數產生 UUID，有一定的重複機率，此機率可以被計算出來
- `uuid5()`：算法與 `uuid3` 相同，只不過哈希函數使用 SHA-1 取代了 MD5。
- `uuid5()`：算法与`uuid3`相同，只不过哈希函数用SHA-1取代了MD5。

由于`uuid4`存在概率型重复，那么在真正需要全局唯一标识符的地方最好不用使用它。在分布式环境下，`uuid1`是很好的选择，因为它能够保证生成ID的全局唯一性。下面是在**Python交互式环境中**使用`uuid1`函数生成全局唯一标识符的例子。

```py
>>> import uuid
>>> uuid.uuid1().hex
'622a8334baab11eaaa9c60f81da8d840'
>>> uuid.uuid1().hex
'62b066debaab11eaaa9c60f81da8d840'
>>> uuid.uuid1().hex
'642c0db0baab11eaaa9c60f81da8d840'
```
