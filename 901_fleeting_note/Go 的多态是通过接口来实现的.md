
```go



// 在 Go 语言中，是没有类似** **extends** **关键字的这种继承的方式，在语言设计上采取的是组合的方式
type Animal struct {
 	Name string
}

type Cat struct {
 	Animal
 	FeatureA string
}

type Dog struct {
 	Animal
 	FeatureB string
}



// Cat 和 Dog 的实例可以拥有自己的方法
// 上述例子能够正常包含调用 Animal 的相关属性和方法，也能够拥有自己的独立属性和方法，在 Go 语言中达到了类似继承的效果
func (dog *Dog) HelloWorld() {
 	fmt.Println("脑子进煎鱼了")
}

func (cat *Cat) HelloWorld() {
 	fmt.Println("煎鱼进脑子了")
}

func main() {
 	p := NewAnimal()
 	p.SetName("我是搬运工，去给煎鱼点赞~")

 	dog := Dog{Animal: *p}
 	fmt.Println(dog.GetName())
}



```



```go


// 我们声明了一个接口类型 AnimalSounder，配套一个 MakeSomeDNA 方法，其接受 AnimalSounder 接口类型作为入参
type AnimalSounder interface {
 	MakeDNA()
}

func MakeSomeDNA(animalSounder AnimalSounder) {
 	animalSounder.MakeDNA()
}


// 只要配套的 Cat 和 Dog 的实例也实现了 MakeSomeDNA 方法，那么我们就可以认为他是 AnimalSounder 接口类型
func (c *Cat) MakeDNA() {
 	fmt.Println("煎鱼是煎鱼")
}

func (c *Dog) MakeDNA() {
 	fmt.Println("煎鱼其实不是煎鱼")
}

func main() {

 	MakeSomeDNA(&Cat{})
 	MakeSomeDNA(&Dog{})
}




```