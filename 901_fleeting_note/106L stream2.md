
ref: 
code: /Users/re4388/project/personal/CS106L/CS106L_re4388/L2_stream2/Makefile

![[IMG-106L stream2-20241003104933512.png|734]]

- cout and cin is buffered
	- it means, i send stuff to a buffer and only flush will go to next place (with some mechanism)
- cout will go to buffer and go to terminal after flush
	- cout and then cin, it will flush
	- cout and sue `endl`(which is a newline chr and a flush)

![[IMG-106L stream2-20241003104959420.png]]

cin: 來源
name: to put to which var
'\n' is optional
`getLine(cin, name, '\n')`
this fn will read until and pass `\n`

getLine and `<<` do not go tohether well, unless u use sth like  `cin.ignore()` to skip one char

![[IMG-106L stream2-20241003105015336.png]]
code at: `/Users/re4388/project/personal/CS106L/CS106L_re4388/L2_stream2/hello.cpp`

if it sucess and remain fail(means no extra can read it) -> mean successd -> return val


## challenge
```c++

// Write the following function which prompts
// the user for a filename, opens the ifstream to
// the file, reprompt if the filename is not valid
// and then return the filename.
string promptUserForFile(ifstream& stream,string prompt = "",string reprompt = "") {
  // your implementation here
}

```

## what is dangling ref?
- a fn return a local variable referenc 
	- when main use this fn, the local var is out of scope -> undefined



## 傳參數一些規則

傳遞參數 p如果 sizeof(p) > 4 * sizeof(int)  -> use reference

當參數 p 的大小超過 4 * sizeof(int) 時，複製這個參數的成本會變得相對較高。這是因為在複製這麼大的數據塊時，會涉及大量的內存操作，這樣會對效能產生不良影響。因此，在這種情況下，建議使用參考（reference）或指標（pointer）來傳遞參數，這樣可以避免不必要的數據複製。

傳遞參數 p 如果 sizeof(p) < 3 * sizeof(int) -> use copy

當參數 p 的大小小於 3 * sizeof(int) 時，使用 const reference 來傳遞參數可能會帶來額外的間接訪問成本。這是因為在這種情況下，參數本身相對夠小，直接複製可以直接放在CPU 的暫存器，不需要放在內存，從而提高執行效能。

ref:  [C++ Core Guidelines: The Rules for in, out, in-out, consume, and forward Function Parameter – MC++ BLOG](https://www.modernescpp.com/index.php/c-core-guidelines-how-to-pass-function-parameters/)


# what is 右值引用（rvalue reference）和 `std::move` 來傳遞和消耗參數


- 右值引用使用 `&&` 表示，它允許你捕捉即將被銷毀的對象（右值），並有效地轉移其資源，而不是進行昂貴的複製操作
- `std::move` 是一個標準庫函數，它將其參數轉換為右值引用。這並不會實際移動任何數據，只是告訴編譯器可以安全地移動這個對象的資源。
- **右值引用**：主要用於實現移動語義（move semantics），可以轉移資源 VS  **一般引用/左值引用**：用於常規引用和修改對象，不涉及資源的轉移。
- rvalue ref 的好處
	- 不需要 copy
	- 允許你重新利用即將被銷毀的對象的資源。例如，對於包含大量動態內存的類型，移動構造函數可以將內部指針移動而不是分配新內存
```cpp

void sink(vector<int>&& v) {   // sink takes ownership of whatever the argument owned
    // usually there might be const accesses of v here
    store_somewhere(std::move(v));
    // usually no more use of v here; it is moved-from
}

```

---

