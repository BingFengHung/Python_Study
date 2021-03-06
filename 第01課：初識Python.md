# 第01課：初識 Python
## Python 簡介
Python 由荷蘭人 Gudio von Rossum 發明的一種程式語言。

**Python 歷史**
1. 1989 年聖誕節：Gudio 開始撰寫 Python 語言的編譯器。
2. 1991 年 2 月：第一個 Python 直譯器誕生，用 C 語言實現的語言，可以調用 C 語言的函式庫
3. 1994 年 1 月：Python 1.0 正式發布
4. 2000 年 10 月：Python 2.0 發布，Python 生態圈開始慢慢成形
5. 2008 年 12 月：Python 3.0 發布，引入許多現代程式語言的新特行，並不完全相容之前的 Python 版本。
6. 2020 年 1 月：Python 2 和 Python 3 共存了 11 年之後，官方停止對 Python 2 的更新與維護，希望使用者盡快遷移至 Python 3。

> 大多數軟體的版本號一般分為三段，如同 A、B、C
> A 表示大版本號，當軟體整體重寫升級或出現不向後相容的變更時，才會增加 A。
> B 表示功能更新，出現新功能時增加 B。
> C 表示小的更動 (例如：修復了某個 Bug)，只要有修改就會增加 C。

## Python 優缺點
**優點：**

1. 簡單明瞭
2. 更少程式碼，提升開發效率
3. 強大的社群與生態圈
4. 可以做的事情非常多 (膠水語言)
   - Web 伺服器開發
   - 雲端建設開發
   - 網路爬蟲
   - 數據分析
   - 量化交易
   - 機器學習、深度學習
   - 自動化測試、自動化運維

**缺點：**

執行效率低，但如果看中開發速度的話而不是執行效率時，Python 就是很好的選擇。

## Python 安裝
- 需要加入 PATH 環境變數
- 安裝 pip 套管理工具
- 指定安裝路徑時，特別注意不要有中文和特殊字形

選擇最新版 Python，安裝完成後，可使用 cmd 輸入 `python --version` 或是 `python -V` 檢查是否安裝成功。
