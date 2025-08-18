---  
alias:  
creation_date: 2024-06-06 18:41  
tags: 
related:
- [[go_lang_idx]] 

---  

Entropy 是一個 CLI 工具，它將掃描您的程式碼庫以查找高熵行，這通常是秘密
https://github.com/EwenQuim/entropy

HK news 討論
https://news.ycombinator.com/item?id=40578060


好奇怎麼算的, 如下
```go

// entropy 函数计算给定文本的熵值
func entropy(text string) float64 {

	// init 一個 map: key is rune, value is struct{}
	// rune is rune 是 Go 語言中用於表示單個 Unicode 字符的資料型別。 與 byte 不同, rune 可以表示多字節的 Unicode 字符。
	uniqueCharacters := make(map[rune]struct{}, len(text))

	// loop over to put all unique characters in the map
	for _, r := range text {
		uniqueCharacters[r] = struct{}{}
	}

	entropy := 0.0
	for character := range uniqueCharacters {
		// 计算 uniqueCharacters 的出现频率
		prob := float64(strings.Count(text, string(character))) / float64(len(text))
		if prob == 0 {
			continue
		}

		// 计算熵值
		// H(X) = -Σ P(x) * log2(P(x))
		// ex:
		// 	H(X) = -[0.5 * log2(0.5) + 0.3 * log2(0.3) + 0.2 * log2(0.2)]
		//   = -[-0.5 - 0.477 - 0.301]
		//   = 1.278 bits

		// 另外
		// 對數函數 log2 可以完成這種測量。以 2 為底的對數,可以直接給出"bit"的度量單位
		// 如果一個隨機變數只有 2 種可能取值,且概率均等(0.5, 0.5)，那麼它的熵就是 log2(2) = 1 bit。這意味著每次觀察這個變數,我們獲得 1 比特的新信息。
		// 如果一個隨機變數有 4 種可能取值,且概率均等(0.25, 0.25, 0.25, 0.25)，那麼它的熵就是 log2(4) = 2 bits。這意味著每次觀察這個變數,我們獲得 2 比特的新信息。
		// 因此如果越多 uniqueCharacters -> entropy 越大
		// 另外如果每一個 uniqueCharacters 的的可能取值越多，entropy 也會是最大的
		entropy -= prob * math.Log2(prob)
	}

	return entropy
}




```