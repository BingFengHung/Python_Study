# 第02課：第一個 Python 程式
## 撰寫程式碼的工具
### 互動式環境
開啟 cmd 輸入 `python` 並按下 enter 即可進入到 Python 互動式環境中。所謂的互動式環境，就是我們輸入一行程式碼按下 Enter 後，
程式碼會立即執行，如果有結果輸出，會立即顯示在畫面上。
```
Python 3.7.6
Type "help", "copyright", "credits" or "license" for more information.
>>> 2 * 3
6
>>> 2 + 3
5
```
要退出互動式環境輸入 `quit()` 即可

### 更好的互動式環境 - IPython 
上面介紹的互動式環境並不那麼有好，可以用 IPython 代替，因為 IPython 提供了更強大的編輯與互動功能。
使用 Python 套件管理工具 `pip` 來安裝 IPython

```
pip install ipython
```

安裝完成後進入 cmd 輸入 ipython 並按下 enter 即可進入
```
ipython
```

### IDE
上面的互動式環境，每次輸入完關閉後，程式碼就不見了，無法重複使用，
大部分的作法都是使用寫一個 python 檔案 (.py) 的方式去執行，下面幾項工具，
就是方便使用的開發環境。
- Visual Studio Code
- jupyternote book
- PyCharm

### 程式執行
執行時，在使用 python xxx.py 即可執行程式

### 程式註解
註解會讓程式碼更容易看懂，但不會影響程式的執行結果

Python 有兩種註解的形式
1. 單行註解：用 `#` 與空白當開頭，可以註解一整行
2. 多行註解：用三個`"""` 開頭，

```py
"""
第一個 Python 程式
"""

# print('hello world')
print('hello world')
```
