Python 是一個直譯式、交互式、動態型別、物件導向的程式語言

1. 直譯式語言 (Interpreted Langauge)
直譯式的編譯方式就是一行一行編譯，不須像編譯式語言 (Compiled Language) 
整個編譯完成才去執行。

可以寫一行編譯一行

2. 交互式程式設計 (Interactive Programming)
由於直譯式的特性 (寫一行編譯一行)，更新程式後，不須重新編譯整個程式
因此，如果前面處裡的資料比較龐大時，不用從頭跑，可以直接修改後面的程式碼
然後執行。

3. 動態型別 (Dynamic Typing)
同一個變數，在執行時，可以被賦予不同型別的值
```python
dynamic = 5
dynamic = "Hello"
print(dynamic) # Hello
```

反之，靜態型別，當型別決定之後，該變數就無法指定為其他型別

4. 物件導向 (Object-oriented programming)
物件導向，就是透過物件本身所提供的介面 (Interface) 來存取 (Access) 物件內部的屬性或方法

## 優點
1. 語法簡單、直覺、可讀性高
2. 龐大第三方函式庫
3. 可調用 C/JAVA
4. 好用的互動式程式介面 (Jupyter)

## 缺點
1. 速度慢 (因為是直譯式需一行編譯一行執行，拖累速度)
2. Python Multithreading
   - 雖然叫做 Multithreading，但是實際上只有一條 Thread 被執行。
   - 詳情請洽 GIL (Global Interpreter Lock)
     - 簡單說就是一把鑰匙，擁有這把鑰匙的 Thread 才能被 CPU 執行。即使是多核CPU，也沒辦法讓多個Thread「並行」地同時執行代碼，只能是交替執行。
