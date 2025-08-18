[[go_lang_idx]]

---


Different from typescript
- no class, use struct and implement interface
- Need to close resource by yourself (defer keyword)
- have low level array
- Have channel
- Have mutex 
- Have go routine
- Can compile to binary directly
- Have pointer to access reference
- shall part more attention on value semantic and reference semantic
- return error and panic (not try-catch)
- function can return multiple value 
- Have type switch
- goto semantic
- select channel syntax



Array is value semantic ( copy by value)
Slice is reference semantic





Language Paradigm:
- TypeScript: 就是 OOP
- Go:
	- 利用其他方式達到類似 OOP
	- 有strcut and method, interface 實作
	- 此用 組合而不是繼承


Classes and Structs:
    
- TypeScript: like Java class
- Go: 沒有 class, 利用 strcut 定義結構，然後可以長出 methods 

		
```go



// 一個 struct
type Mail struct {
	mail string
}

// 一個介面，可以被多個 strcut 給實作
type Sender interface {
	Send()
}


// Mail struct 實作 Sender 介面
func (mail Mail) Send() {
	fmt.Println(mail.mail)
}

func Run0() {
	var sender Sender = Mail{"hello"}
	sender.Send()
}


```



Inheritance繼承:
    
- TypeScript:  single inheritance using the `extends` keyword.
- Go: Go does not have built-in support for inheritance. Instead, it encourages composition and code reuse through struct embedding and interfaces.


Polymorphism:

- TS: 透過類似 java 的 sub-class
- Go: 透過實作 interface




Type System:
    
- 動態語言包一層的type ， 很多檢查不到 xd
- go → compiled lang, 靜態語言



co-current primitive
    
- go:
	- channel, mutex, goroutine(更輕量的 thread)
	- 重點放在多個輕量 go routine 的協同作業
- JS/TS:
	- promise, worker thread process
	- 重點放在 async code (利用底層的 event loop), callback