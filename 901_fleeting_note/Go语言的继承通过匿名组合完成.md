[[go_lang_idx]]



在 Go 言語（Golang）中，沒有傳統意義上的“繼承”機制

Go 中沒有像其他面向對象語言一樣的類和子類之間的繼承關係。但 Go 提供了一個非常靈活的方式來實現「組合和重用」：即匿名結構體（anonymous struct）的組合。

這種“繼承”的方式是通過將一個結構體嵌入到另一個結構體中，而不必明確指出嵌入的是哪個結構體。
這被稱為基於組合的編程（composition over inheritance）。

當你將一個結構體作為另一個結構體的一部分嵌入時，不需要給它一個具名字段，這樣嵌入的結構體成為匿名字段。
使得外層結構體“繼承”了內層結構體的所有字段和方法。

### 示例說明

#### 定義基本結構體和其方法

```go

package main

import "fmt"

// Animal 是一個基本的結構體，表示動物。
type Animal struct {
	Name string
}

// Move 是 Animal 的一個方法。

func (a Animal) Move() {
	fmt.Printf("%s is moving\n", a.Name)
}

```

#### 匿名嵌入基本結構體進行“繼承”

```go

// Dog 是一個新的結構體，它匿名組合了 Animal。

type Dog struct {
	Animal
	Breed string
}

// 狗有自己獨特的方法

func (d Dog) Bark() {
	fmt.Printf("%s is barking\n", d.Name)
}

```

在這個例子中，`Dog` 結構體通過匿名地組合 `Animal` 結構體，相當於“繼承”了 `Animal` 的所有字段和方法
這裡的 `Move` 方法會自動成為 `Dog` 的一部分。

#### 使用範例

```go

func main() {

// 創建 Dog 的實例

myDog := Dog{
	Animal: Animal{Name: "Buddy"},
	Breed: "Golden Retriever",
}

// 調用 Animal 的方法
myDog.Move() // 輸出: Buddy is moving

// 調用 Dog 自己的方法
myDog.Bark() // 輸出: Buddy is barking

}

```

### 結論

在上述例子中，我們沒有通過傳統的“類繼承”來擴展功能，而是通過匿名地組合其他結構體來達成類似效果。這種方式具有以下優點：

1. **靈活性高**：

- 組合允許你選擇任意多的結構體進行嵌入，而無需受限於單一繼承。

2. **更加組件化**：

- 組合強調了組件化的設計理念，使系統的組件更加分離和組合。

3. **代碼簡潔**：

- 由於匿名組合，內部結構體的字段和方法自動成為外部結構體的一部分，簡化代碼的同時提供了封裝和重用功能。

4. **避免繁瑣的繼承層級**：

- 通過組合而非複雜的多層繼承，代碼結構更加清晰簡單。

通過這樣的設計，Go 保持了簡潔性與靈活性，同時提供了一種高效的代碼重用方式。


衍生: [怎么在golang中通过接口嵌套实现复用 - 开发技术 - 亿速云](https://www.yisu.com/jc/452409.html)