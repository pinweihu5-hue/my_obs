---


---

[It’s not wrong that "🤦🏼‍♂️".length == 7](https://hsivonen.fi/string-length/)
為什麼相同內容的字串在不同語言中會被 `.length` 計算出不同的結果?
- 不同語言選擇用不同層級來實作 `.length`，並不代表錯，僅代表設計取捨不同
	- 記憶體儲存的 **code units**（bytes 或 UTF-16 units）
	- Unicode 的 **code points**
	- 使用者「視覺上」認知的一個符號（**extended grapheme cluster**）


