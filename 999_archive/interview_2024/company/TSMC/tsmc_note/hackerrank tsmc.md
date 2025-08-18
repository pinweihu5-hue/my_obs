



# Q1 變形背包問題


![[IMG-hackerrank tsmc-20250810201324028.png|739]]

![[IMG-hackerrank tsmc-20250810201327981.png]]


```js fold


function calculateMaximumProfit(cost, x) {
    let mod = 10**9 + 7
    let mem = {}
    return dfs(0, 0)
    function dfs(i, sum){
        let key = i + ',' + sum
        if(key in mem) return mem[key]
        if(sum === x) return 0
        if(i === cost.length) return 0
        
        let buyProfit = dfs(i+1, sum + cost[i]) + (2**i) % mod
        let notBuyProfit = dfs(i+1, sum)
        
        let curMaxProfit = Math.max(buyProfit, notBuyProfit)
        mem[key] = curMaxProfit
        return mem[key]
    }

}


```



ai dp solve
```js fold

function calculateMaximumProfit(cost, x) {
    const n = cost.length;
    const mod = 10**9 + 7;
    const dp = Array(x + 1).fill(0); // dp[j] stores max profit with money j

    for (let i = 0; i < n; i++) {
        const profit = 2**i;
        for (let j = x; j >= cost[i]; j--) {
            dp[j] = Math.max(dp[j], (dp[j - cost[i]] + profit) % mod);
        }
    }

    return dp[x];
}

```


ai topDown solve
```js fold

function calculateMaximumProfit(cost, x) {
    const n = cost.length;
    const mod = 10**9 + 7;
    const memo = new Map(); // For memoization

    function dp(i, currentMoney) {
        if (currentMoney > x) {
            return 0; // Cannot exceed budget
        }
        if (i === n) {
            return 0; // No more items
        }

        const key = `${i},${currentMoney}`;
        if (memo.has(key)) {
            return memo.get(key);
        }

        let profit = 0;
        // Option 1: Buy the current item
        if (currentMoney + cost[i] <= x) {
            profit = (2**i + dp(i + 1, currentMoney + cost[i])) % mod;
        }

        // Option 2: Don't buy the current item
        profit = Math.max(profit, dp(i + 1, currentMoney));

        memo.set(key, profit);
        return profit;
    }

    return dp(0, 0);
}


```


## 時間複雜度分析

如果沒有使用 memoization:
這個遞迴函式的時間複雜度會是 **O(2<sup>n</sup>)
在每個遞迴層級，`dp` 函式都有兩個分支：
1. **購買物品：** `dp(i + 1, currentMoney + cost[i])`
2. **不購買物品：** `dp(i + 1, currentMoney)`
這表示在每一層，呼叫次數都會加倍。由於有 `n` 個物品，遞迴樹的深度最多為 `n`。因此，總呼叫次數大致上會是 2<sup>0</sup> + 2<sup>1</sup> + 2<sup>2</sup> + ... + 2<sup>n</sup>，這是一個幾何級數，其總和約為 2<sup>n+1</sup> - 1，因此時間複雜度是 O(2<sup>n</sup>)。

如果使用memoization:
由於每個子問題最多只會被計算一次，而子問題的總數最多為 n * x 個 (因為 i 的範圍是 0 到 n-1，currentMoney 的範圍是 0 到 x)，因此整體時間複雜度就變成了 O (n*x)。



## 空間複雜度
O (n*x)
memo Map： memo Map 最多會儲存 n * x 個子問題的結果，因此空間複雜度為 O (n*x)。
遞迴呼叫堆疊： 在最壞的情況下，遞迴呼叫的深度可以達到 n (當所有物品都被考慮時)。因此，遞迴呼叫堆疊的空間複雜度為 O (n)。


總結:
時間複雜度: O (n*x)
空間複雜度: O (n*x)

## 優化空間複雜度
如果想優化空間複雜度，可以使用 bottom-up 的動態規劃方法，這樣可以避免遞迴呼叫堆疊的開銷，將空間複雜度降至 O (x)。 
Bottom-up 的方法使用一個二維陣列或兩個一維陣列來儲存子問題的結果，並以迭代的方式計算。

希望這個分析對你有所幫助！

---


你說得對，從時間複雜度的角度來看，top-down with memoization 和 bottom-up dynamic programming 通常是一樣的。然而，實際執行時間 top-down 卻常常比較慢，主要原因的確是函式呼叫的額外開銷 (overhead)，尤其是在遞迴深度很大的情況下。

以下是幾個造成 top-down 方法較慢的因素：

* **函式呼叫的額外開銷：** 每次函式呼叫都需要建立新的堆疊框架 (stack frame)，儲存函式的區域變數、參數、以及返回地址。  這些操作會消耗時間和記憶體。  在遞迴深度很大的情況下，這些額外開銷會累積起來，導致執行時間變慢。
* **遞迴的本質：** 遞迴本身就比迭代 (iteration) 慢。  這是因為遞迴需要維護呼叫堆疊，而迭代則不需要。  雖然 memoization 可以減少遞迴呼叫的次數，但它並不能完全消除遞迴的額外開銷。
* **快取未命中 (Cache Miss)：**  Top-down 方法的記憶體存取模式可能不如 bottom-up 方法規律。  在 bottom-up 方法中，通常會以循序的方式存取 `dp` 陣列，這有利於 CPU 快取的利用。  而 top-down 方法的遞迴呼叫可能會導致更頻繁的快取未命中，從而降低效能。
* **編譯器優化：**  編譯器通常可以更好地優化 bottom-up 方法的程式碼，例如迴圈展開 (loop unrolling) 和向量化 (vectorization)。  而 top-down 方法的遞迴程式碼比較難以優化。


**比較 Top-down 和 Bottom-up：**

| 特性     | Top-down (Memoization) | Bottom-up   |
| ------ | ---------------------- | ----------- |
| 時間複雜度  | 通常與 bottom-up 相同       |             |
| 空間複雜度  | 可能因為遞迴堆疊略高             | 通常較低        |
| 程式碼簡潔度 | 通常較簡潔易懂                | 可能較複雜       |
| 執行效率   | 通常較慢 (函式呼叫額外開銷)        | 通常較快        |
| 除錯難易度  | 遞迴呼叫可能較難除錯             | 迭代程式碼通常較易除錯 |

**結論：**
雖然 top-down with memoization 在時間複雜度上與 bottom-up 相同，但由於函式呼叫的額外開銷、遞迴的本質、快取未命中以及編譯器優化的差異，實際執行時間通常會比較慢。  在效能要求非常高的情況下，建議優先考慮 bottom-up 方法。  然而，如果程式碼簡潔度和易懂性更重要，則 top-down 方法也是一個不錯的選擇。





# Q2 解密圓圈
![[IMG-hackerrank tsmc-20250810201329193.png]]



![[IMG-hackerrank tsmc-20250810201329734.png]]

![[IMG-hackerrank tsmc-20250810201330058.png]]


```js title:explainVersion fold

function simpleCipher(encrypted, k) {
    let res = ''

    // 可能會很大，要 mod 26
    let k1 = k % 26
    console.log('k1 -->', k1) // 7

    let baseASCII = 'A'.charCodeAt(0)
    console.log('baseASCII -->', baseASCII) // 65

    for (let i = 0; i < encrypted.length; i++) {


         // char  -> ascii, like A 就是 '65'
        let charCode = encrypted.charCodeAt(i)
        console.log('charCode -->', charCode) // Z -> 90

        ///////////////////////////////////////////////////////////  進入 26 base
        // ascii -> 26 base
        let difFromA = charCode - baseASCII // 25, 第 25 個

        // 這個可能未必要做? 因為等等要 mod 26, 怕會有 negative
        let charCodeFromA = difFromA + 26
        console.log('charCodeFromA -->', charCodeFromA) // 46

        // shift k1
        let charCodeFromAOffsetByK = charCodeFromA - k1
        console.log('charCodeFromAOffsetByK -->', charCodeFromAOffsetByK) // 39

        // wrap around
        let wrappedAround = charCodeFromAOffsetByK % 26
        console.log('wrappedAround -->', wrappedAround) // 13
        //////////////////////////////////////////////////////////////// exit 26 base

        // 26 base -> ascii
        let decryptedCharCode = baseASCII + wrappedAround
        console.log('decryptedCharCode -->', decryptedCharCode) // 78

        // ascii -> char
        let decrypted = String.fromCharCode(decryptedCharCode)
        console.log('decrypted -->', decrypted)
        res += decrypted
    }
    return res
}


```

```js title:shortVersion fold


function simpleCipher(encrypted, k) {
    let decrypted = ''
    let k1 = k % 26
    for (let i = 0; i < encrypted.length; i++) {
        let charCode = encrypted.charCodeAt(i)

        let decryptedCharCode = 'A'.charCodeAt(0) + ((charCode - 'A'.charCodeAt(0) - k1 + 26) % 26) // Uppercase
        decrypted += String.fromCharCode(decryptedCharCode)
    }
    return decrypted
}

console.log(simpleCipher('UGBQTFOTHJ', 2)) // Correct output: SECNURMNGH
console.log(simpleCipher('ZABC', 1)) // Correct output: YZAB




```

TIL
- k 會很大，也要 mod 26
- 需了解以下操作
	- char -> ascii, `charCodeAt`
	- 轉換：ascii -> 'A' base  和  base'A' -> ascii
		- 就是用這個 char 的ascii 減掉 A 的ascii
		- 加回來
	- ascii -> char,  `String.fromCharCode`
- `-k` 之前，我們 + 26, 以免 -k 變成 negative


### 時間複雜度

1. **迴圈**:
    - 代碼中有一個 `for` 迴圈，這個迴圈的次數是 `encrypted.length`，即加密字符串的長度。假設加密字符串的長度為 (n)，那麼這個迴圈的時間複雜度是 ( O (n) )。
2. **每次迴圈內的操作**:
    
    - 在每次迴圈中，執行的操作包括：
        - 獲取字符的 ASCII 值 (`charCodeAt`): ( O(1) )
        - 計算字符與 'A' 的差值: (O (1) )
        - 計算偏移量: (O (1) )
        - 計算模運算: (O (1) )
        - 轉換回 ASCII 值 (`fromCharCode`): ( O(1) )
        - 字符串拼接: (O (1) )（在 JavaScript 中，字符串是不可變的，拼接操作在內部可能會涉及到創建新字符串，但在這裡我們可以簡化為 ( O (1) ) 的操作，因為我們只是在拼接一個字符）
    
    因此，每次迴圈內的操作都是常數時間 (O (1) )。
    

### 總結時間複雜度
綜合以上分析，整個函數的時間複雜度是 (O (n) )，其中 ( n ) 是加密字符串的長度。

### 空間複雜度
1. **輸入和輸出**:
    - 函數的輸入是 `encrypted` 字符串和整數 `k`，這些都不會影響空間複雜度的計算。
    - 輸出是 `res` 字符串，這個字符串的長度與 `encrypted` 字符串相同，因此在最壞情況下，`res` 需要 (O (n) ) 的空間來存儲解密後的字符串。
2. **其他變量**:
    - 其他變量（如 `k1`, `baseASCII`, `charCode`, `difFromA`, `charCodeFromA`, `charCodeFromAOffsetByK`, `wrappedAround`, `decryptedCharCode`, `decrypted`）都是常數空間 (O (1) )。

### 總結空間複雜度
因此，整個函數的空間複雜度是 (O (n) )，因為我們需要存儲解密後的字符串。

### 最終結論

- **時間複雜度**: ( O(n) )
- **空間複雜度**: ( O(n) )

這段代碼的效率在處理長字符串時是相對可接受的，因為它的時間和空間複雜度都是線性的。



# Q3 求 unique pali sub str 的數量

給定一個 string
求這個 string 裡面有多少 pali substring

![[IMG-hackerrank tsmc-20250810201330360.png|738]]

![[IMG-hackerrank tsmc-20250810201330514.png]]

![[IMG-hackerrank tsmc-20250810201330662.png]]



```js fold

function palindrome(s) {
    const res = new Set() // Use a Set to store unique palindromes

    // 這個迴圈迭代字串 s 中的每個字元，將每個字元都當作潛在回文的中心點來檢查。
    for (let i = 0; i < s.length; i++) {

        // 從中心點 i 向兩邊擴展，檢查奇數長度的回文 (例如 "aba", "racecar")。
        // 另外有兩個條件， i-j >=0 and i+j < s.length 是因為下面要 s[i-j] and s[i+j], 那如果不要噴掉，這個條件一定要加上
        // 一邊是>=0, 一邊是 < s.length
        for (let j = 0; i - j >= 0 && i + j < s.length; j++) {


            // 比較 odd, 當 i = 1, j = 0 or 2 是否一樣
            //   i
            //  j j
            // 'aba'
            //  012

            if (s[i - j] === s[i + j]) {
                res.add(s.substring(i - j, i + j + 1)) // 多一個idx, 因為後面不會包括
            } else {
                break // No longer a palindrome
            }
        }

        // 從中心點 i 和 i+1 向兩邊擴展，檢查偶數長度的回文 (例如 "abba", "noon")。



        // 比較 even, 當 i = 1, j= 0 or 3 是否一樣
        //   i
        //  j  j
        // 'abba'
        //  0123

        // 另外有兩個條件， i-j >=0 and i+j+1 < s.length 是因為下面要 s[i-j] and s[i+j+1], 那如果不要噴掉，這個條件一定要加上
        // 一邊是>=0, 一邊是 < s.length
        for (let j = 0; i - j >= 0 && i + j + 1 < s.length; j++) {
            if (s[i - j] === s[i + j + 1]) {
                res.add(s.substring(i - j, i + j + 2)) // 多一個idx, 因為後面不會包括
            } else {
                break // No longer a palindrome
            }
        }
    }

    return res.size
}


```



讓我們分析這段 JavaScript 代碼的時間和空間複雜度。這段代碼的目的是找出字符串中的所有獨特回文子串，並返回它們的數量。

### 時間複雜度
1. **外層迴圈**:
    - 外層迴圈 `for (let i = 0; i < s.length; i++)` 會遍歷字符串的每個字符，這個迴圈的時間複雜度是 (O (n) )，其中 ( n ) 是字符串的長度。
2. **內層迴圈**:
    - 內層有兩個迴圈，分別用來檢查奇數長度和偶數長度的回文。
    - 在最壞情況下，對於每個字符 (i)，內層迴圈可能會遍歷整個字符串。這意味著在某些情況下，內層迴圈的時間複雜度可以達到 ( O (n) )。
    - 因此，對於每個字符 (i)，最壞情況下的時間複雜度是 ( O (n) )（對於每個字符，內層迴圈可能會執行最多 ( n ) 次）。
3. **總時間複雜度**:
    - 因此，整體的時間複雜度是 (O (n^2) )，因為外層迴圈 ( O (n) ) 和內層迴圈的最壞情況 ( O (n) ) 相乘。

### 空間複雜度
1. **使用的數據結構**:
    - 代碼中使用了一個 `Set` 來存儲獨特的回文子串。最壞情況下，所有的子串都是回文，這意味著 `Set` 可能會存儲 (O (n^2) ) 個子串（例如，對於字符串 "aaaa" 來說，所有的子串都是回文）。
2. **其他變量**:
    - 其他變量（如 `i`, `j`）都是常數空間 (O (1) )。

### 總結空間複雜度
因此，整個函數的空間複雜度是 (O (n^2) )，因為 `Set` 可能會存儲最多 (O (n^2) ) 個獨特的回文子串。

### 最終結論

- **時間複雜度**: ( O(n^2) )
- **空間複雜度**: ( O(n^2) )

這段代碼的效率在處理長字符串時可能會受到影響，因為它的時間和空間複雜度都是二次方的。對於較長的字符串，可能需要考慮更高效的算法來解決回文子串的問題，例如使用動態規劃或中心擴展法。




# 考試規則
---

Hello,

感謝您參加台積電 IT 單位面試，我們邀請您參與測驗，請務必於限定的時間之內完成此線上程式測驗，點擊底下的 Start Test 後，會有測驗的詳細說明和考試環境的試用，待您確認完後才會開始正式考試。

測驗一旦計時開始後便無法暫停，請留下連續 2 小時以上的時間，並且於網路順暢的環境中進行測驗。  
  
測驗過程會被全程紀錄，測驗內容請保密，禁止截圖或是錄影將考試題目外流。

為避免舞弊狀況，測驗過程將會被全程紀錄。測驗前請您先打開 Webcam，從您開始作答至提交答案，Webcam 每隔一分鐘會進行拍照，以確保為您本人親自作答。若有任何問題，請詳閱隱私權政策之說明。

測驗中如遇到突發狀況，例如電腦當機、斷線等等，請再點一次連結、或是重新整理，就可以從中斷的地方繼續考試，但計時不會中斷。如果有問題，請聯絡和您安排面試的  HR 同仁，請勿直接回覆此信件。

Good luck!

## Instructions

1. 這是線上程式測驗。如果是第一次使用本系統，**請先試試** [**Sample Test**](https://www.hackerrank.com/tests/143hd7jsid6/1c1521f6f2821d9e07525fa45a2a8b27) **以熟悉測驗環境**。Sample Test 並不會佔用實際測驗時間，請確認已經了解相關功能 (瀏覽題目、選擇程式語言、撰寫程式、測試或提交程式... 等等) 再開始正式測驗。**系統操作有問題請於** **Sample Test** **時提出**。
2. 測驗前請您先打開 Webcam，從您開始作答到提交答案，Webcam 每隔一分鐘會進行拍照，以確保為您本人親自作答。請詳閱[隱私權政策](https://www.hackerrank.com/privacy)之說明**。**
3. 由於系統作答區跟中文輸入法有些衝突，請留意不要不小心打到中文，儘量換成英文輸入法再作答。
4. 支援的程式語言列在下方，請選擇自己最熟悉的使用即可。
5. 考試時**禁止和其他人討論題目**。可以上網查詢程式語言相關說明及文件，但系統會偵測程式是否是從網路上複製貼上來的，**請勿抄襲，請勿用不正當的方式取得答案。**
6. **請勿洩漏題目給其他人**，以確保考試公平性。
7. **測驗時間為** **90** **分鐘。題目共有** **3** **題**，通常越後面越難，建議先大致瀏覽所有題目後，先從自己有把握的作答。不需要拿到滿分就能通過測驗。
8. 作答程式區通常已經提供 main function, header 等等，只需要實作題目要求的 function。
9. 如果題目要求撰寫完整的程式，請詳細閱讀題目要求的格式。所有的 input 都吃 STDIN，並且 output 到 STDOUT。如果使用 Java 程式語言，請用 Solution 當成 class 名稱。
10. 每一題有多個測資，答對每個測資都有部份分數。大部份的測資都是隱藏的，請確保撰寫的程式能儘量考慮不同的狀況。**請勿以將測資貼進程式中判斷的方式作答。**
11. 提交程式前，可以先試跑目前的程式，所有 test case 都會跑過一次。如果 test case 全數通過，代表這題已經拿到滿分，可以放心提交程式。
12. 可善用 print console 來 debug 你的程式 (例如 C++ 的 cout, C 的 printf 等等) 。
13. 如果考試中途因為網路或電腦當機導致中斷，重新點擊考試連結登入即可從中斷的地方繼續，但計時不會暫停。
14. 更多詳細說明，請參考系統官方的 [FAQ](https://www.hackerrank.com/tests/info/faq)。
15. **一旦當你開始正式考試，即視為你同意以上條件，若你仍然有任何問題，勿直接作答，請先聯絡和您安排面試的  HR 同仁。**