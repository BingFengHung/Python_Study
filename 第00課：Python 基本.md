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
