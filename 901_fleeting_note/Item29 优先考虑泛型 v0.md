Favor generic types



### a generic types for stack
```java

// Object-based collection - a prime candidate for generics
public class Stack {
   private Object[] elements;
   private int size = 0;
   private static final int DEFAULT_INITIAL_CAPACITY = 16;

   public Stack() {
      elements = new Object[DEFAULT_INITIAL_CAPACITY];
   }

   public void push(Object e) {
      ensureCapacity();
      elements[size++] = e;
   }

   public Object pop() {
      if (size == 0) throw new EmptyStackException();
      Object result = elements[--size];
      elements[size] = null; // Eliminate obsolete reference return result;
   }

   public boolean isEmpty() {
      return size == 0;
   }

   private void ensureCapacity() {
      if (elements.length == size) elements = Arrays.copyOf(elements, 2 * size + 1);
   }
}

```


泛型應該是不會導致原本的 client 需要修改
另外原本的client 如果濫用，本身也已經 cast 且有 runtime err 的風險了

let's try

the conventional name for this type parameter is E (Item 68)

基本上 你把 Object 換成 E

你會遇到這個錯誤
![[IMG-Item29 优先考虑泛型 v0-20250805201147111.png]]

基本上，你無法建立 arr of 泛型

你有兩個解
第一個是 cast
但是你會有 warning 如下
![[IMG-Item29 优先考虑泛型 v0-20250805201147739.png]]


這邊，你得自己去檢查是否真的 type safe
以這題來說，arry 是 private field, 然後也不會 ret 給 client 或是給其他 method
有可能把東西存到arr的是 push
然後 push 進來我們已經有限制是 E type, 因此這個 uncheced cast 應該是安全的!!


那我們應該加上 comment and suppress it, like below
```java

    // The elements array will contain only E instances from push(E).
    // This is sufficient to ensure type safety, but the runtime
    // type of the array won't be E[]; it will always be Object[]!
    @SuppressWarnings("unchecked")
    public Stack() {
        elements = (E[])new Object[DEFAULT_INITIAL_CAPACITY];
    }

```



第二的解法是把這邊從 E 改為 Object
![[IMG-Item29 优先考虑泛型 v0-20250805201148386.png]]


變成另一個 err
![[IMG-Item29 优先考虑泛型 v0-20250805201149157.png]]


可以用 casting, 變成 warning
![[IMG-Item29 优先考虑泛型 v0-20250805201149999.png]]


這邊一樣要自己確認是否 type safe
這邊一樣是 safe, 道理一樣，都會是 E
因此我們加上 supress 另外和 comment
![[IMG-Item29 优先考虑泛型 v0-20250805201150940.png]]


Both techniques for eliminating the generic array creation have their adherents. 

### 兩個方法都有擁護者
方法一比較好讀，初始化的地方 cat 一次即可
方法二可能要 cast 所有 arr element 被讀的地方 -> 因此很多人都用方法1

不過方法一 cause heap pollution (Item 32): the runtime type of the array does not match its compile-time type (unless E happens to be Object), 不過這邊並沒有 heap pollution 的危害


--


### 不是說prefer list over arr?

因為 java 對 lists 的原生支持可能還輸給 arr

for performace reason, 一些泛型的 type 像是 ArrayList 也是實作在 arr 之上，或是 HashMap 也是實作於 arr 上 

不過很多時候，像是這個 Stack, 你不需要建立在 arr 上，你可以建立 like `Stack<Object>`, `Stack<int[]>`, `Stack<List<String>>`, or `Stack` of any other object reference type. 

對了，你無法 type parameter 放原生 type, like `Stack<int>` or `Stack<double>` -> compile err 
(除非你用 boxed primitive types (Item 61).)


### bounded type parameter

也有一些泛型對於參數有使用限制，像是 `java.util.concurrent.DelayQueue`, whose declaration looks like this:

`class DelayQueue<E extends Delayed> implements BlockingQueue<E>`

其泛型 (`<E extends Delayed>`)  規定 E 一定是 `java.util.concurrent.Delayed` 的 subtype

透過這個限制，基本上可以remove casting 的需要 和 ClassCastException 的 exception 

這種 type parameter `E` is known as a bounded type parameter. 
Note that the subtype relation is defined so that every type is a subtype of itself [JLS, 4.10], 
so it is legal to create a `DelayQueue<Delayed>`.


### summary
- generic types are safer and easier to use than types that require casts in client code. 
- When you design new types, make sure that they can be used without such casts. This will often mean making the types generic. 
- If you have any existing types that should be generic but aren’t, generify them. 
- This will make life easier for new users of these types without breaking existing clients (Item 26).