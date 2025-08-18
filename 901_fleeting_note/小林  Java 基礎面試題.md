## 概念

### [#](https://xiaolincoding.com/interview/java.html#%E8%AF%B4%E4%B8%80%E4%B8%8Bjava%E7%9A%84%E7%89%B9%E7%82%B9)說一下 Java 的特點

主要有以下的特點：

- **平台無關性**：Java 的 “編寫一次，運行無處不在” 哲學是其最大的特點之一。Java 編譯器將源代碼編譯成字節碼（bytecode），該字節碼可以在任何安裝了 Java 虛拟機（JVM）的系統上運行。
- **面向對象**：Java 是一門嚴格的面向對象編程語言，幾乎一切都是對象。面向對象編程（OOP）特性使得代碼更易於維護和重用，包括類（class）、對象（object）、繼承（inheritance）、多态（polymorphism）、抽象（abstraction）和封裝（encapsulation）。
- **内存管理**：Java 有自己的垃圾回收機制，自動管理内存和回收不再使用的對象。這樣，開發者不需要手動管理内存，從而減少内存洩漏和其他内存相關的問題。

### [#](https://xiaolincoding.com/interview/java.html#java%E4%B8%BA%E4%BB%80%E4%B9%88%E6%98%AF%E8%B7%A8%E5%B9%B3%E5%8F%B0%E7%9A%84)Java 為什麽是跨平台的？

Java 能支持跨平台，主要依賴於 JVM 關系比較大。

JVM 也是一個軟件，不同的平台有不同的版本。我們編寫的 Java 源碼，編譯後會生成一種 .class 文件，稱為字節碼文件。Java 虛拟機就是負責將字節碼文件翻譯成特定平台下的機器碼然後運行。也就是說，隻要在不同平台上安裝對應的 JVM，就可以運行字節碼文件，運行我們編寫的 Java 程序。

而這個過程中，我們編寫的 Java 程序沒有做任何改變，僅僅是通過 JVM 這一” 中間層 “，就能在不同平台上運行，真正實現了” 一次編譯，到處運行 “的目的。

JVM 是一個” 橋梁 “，是一個” 中間件 “，是實現跨平台的關鍵，Java 代碼首先被編譯成字節碼文件，再由 JVM 將字節碼文件翻譯成機器語言，從而達到運行 Java 程序的目的。

編譯的結果不是生成機器碼，而是生成字節碼，字節碼不能直接運行，必須通過 JVM 翻譯成機器碼才能運行。不同平台下編譯生成的字節碼是一樣的，但是由 JVM 翻譯成的機器碼卻不一樣。

所以，運行 Java 程序必須有 JVM 的支持，因為編譯的結果不是機器碼，必須要經過 JVM 的再次翻譯才能執行。即使你將 Java 程序打包成可執行文件（例如 .exe），仍然需要 JVM 的支持。

跨平台的是 Java 程序，不是 JVM。JVM 是用 C/C++ 開發的，是編譯後的機器碼，不能跨平台，不同平台下需要安裝不同版本的 JVM。

![[IMG-小林  Java 基礎面試題-20250222193717508.png]]

### [#](https://xiaolincoding.com/interview/java.html#jvm%E3%80%81jdk%E3%80%81jre%E4%B8%89%E8%80%85%E5%85%B3%E7%B3%BB)JVM、JDK、JRE 三者關系？

![[100_attachements/3dac66aa01f14fbe49a1f39fc1ab5c03_MD5.webp]]

它們之間的關系如下：

- JVM 是 Java 虛拟機，是 Java 程序運行的環境。它負責將 Java 字節碼（由 Java 編譯器生成）解釋或編譯成機器碼，并執行程序。JVM 提供了内存管理、垃圾回收、安全性等功能，使得 Java 程序具備跨平台性。
- JDK 是 Java 開發工具包，是開發 Java 程序所需的工具集合。它包含了 JVM、編譯器（javac）、調試器（jdb）等開發工具，以及一系列的類庫（如 Java 標準庫和開發工具庫）。JDK 提供了開發、編譯、調試和運行 Java 程序所需的全部工具和環境。
- JRE 是 Java 運行時環境，是 Java 程序運行所需的最小環境。它包含了 JVM 和一組 Java 類庫，用於支持 Java 程序的執行。JRE 不包含開發工具，隻提供 Java 程序運行所需的運行環境。

### [#](https://xiaolincoding.com/interview/java.html#%E4%B8%BA%E4%BB%80%E4%B9%88java%E8%A7%A3%E9%87%8A%E5%92%8C%E7%BC%96%E8%AF%91%E9%83%BD%E6%9C%89)為什麽 Java 解釋和編譯都有？

首先在 Java 經過編譯之後生成字節碼文件，接下來進入 JVM 中，就有兩個步驟編譯和解釋。 如下圖：

![[100_attachements/1482951c4f228303b5a8c7e8ce134561_MD5.webp]]

**編譯性**：

- Java 源代碼首先被編譯成字節碼，JIT 會把編譯過的機器碼保存起來，以備下次使用。

**解釋性：**

- JVM 中一個方法調用計數器，當累計計數大於一定值的時候，就使用 JIT 進行編譯生成機器碼文件。否則就是用解釋器進行解釋執行，然後字節碼也是經過解釋器進行解釋運行的。

所以 Java 既是編譯型也是解釋性語言，默認采用的是解釋器和編譯器混合的模式。

### [#](https://xiaolincoding.com/interview/java.html#jvm%E6%98%AF%E4%BB%80%E4%B9%88)jvm 是什麽

JVM 是 java 虛拟機，主要工作是解釋自己的指令集（即字節碼）并映射到本地的 CPU 指令集和 OS 的系統調用。

JVM 屏蔽了與操作系統平台相關的信息，使得 Java 程序隻需要生成在 Java 虛拟機上運行的目標代碼（字節碼），就可在多種平台上不加修改的運行，這也是 Java 能夠 “**一次編譯，到處運行的**” 原因。

### [#](https://xiaolincoding.com/interview/java.html#%E7%BC%96%E8%AF%91%E5%9E%8B%E8%AF%AD%E8%A8%80%E5%92%8C%E8%A7%A3%E9%87%8A%E5%9E%8B%E8%AF%AD%E8%A8%80%E7%9A%84%E5%8C%BA%E5%88%AB)**編譯型語言和解釋型語言的區别？**

編譯型語言和解釋型語言的區别在於：

- 編譯型語言：在程序執行之前，整個源代碼會被編譯成機器碼或者字節碼，生成可執行文件。執行時直接運行編譯後的代碼，速度快，但跨平台性較差。
- 解釋型語言：在程序執行時，逐行解釋執行源代碼，不生成獨立的可執行文件。通常由解釋器動态解釋并執行代碼，跨平台性好，但執行速度相對較慢。
- 典型的編譯型語言如 C、C++，典型的解釋型語言如 Python、JavaScript。

### [#](https://xiaolincoding.com/interview/java.html#python%E5%92%8Cjava%E5%8C%BA%E5%88%AB%E6%98%AF%E4%BB%80%E4%B9%88)Python 和 Java 區别是什麽？

- Java 是一種已編譯的編程語言，Java 編譯器將源代碼編譯為字節碼，而字節碼則由 Java 虛拟機執行
- python 是一種解釋語言，翻譯時會在執行程序的同時進行翻譯。

## [#](https://xiaolincoding.com/interview/java.html#%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)數據類型

### [#](https://xiaolincoding.com/interview/java.html#%E5%85%AB%E7%A7%8D%E5%9F%BA%E6%9C%AC%E7%9A%84%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)八種基本的數據類型

Java 支持數據類型分為兩類： 基本數據類型和引用數據類型。

基本數據類型共有 8 種，可以分為三類：

- 數值型：整數類型（byte、short、int、long）和浮點類型（float、double）
- 字符型：char
- 布爾型：boolean

![[IMG-小林  Java 基礎面試題-20250222193846562.png]]

8 種基本數據類型的默認值、位數、取值範圍，如下表所示：
![[IMG-小林  Java 基礎面試題-20250807203909891.png]]
Float 和 Double 的最小值和最大值都是以科學記數法的形式輸出的，結尾的 “E + 數字” 表示 E 之前的數字要乘以 10 的多少倍。比如 3.14E3 就是 3.14×1000=3140，3.14E-3 就是 3.14/1000=0.00314。

注意一下幾點：

- java 八種基本數據類型的字節數：1 字節 (byte、boolean)、 2 字節 (short、char)、4 字節 (int、float)、8 字節 (long、double)
- 浮點數的默認類型為 double（如果需要聲明一個常量為 float 型，則必須要在末尾加上 f 或 F）
- 整數的默認類型為 int（聲明 Long 型在末尾加上 l 或者 L）
- 八種基本數據類型的包裝類：除了 char 的是 Character、int 類型的是 Integer，其他都是首字母大寫
- char 類型是無符號的，不能為負，所以是 0 開始的

### [#](https://xiaolincoding.com/interview/java.html#long%E5%92%8Cint%E5%8F%AF%E4%BB%A5%E4%BA%92%E8%BD%AC%E5%90%97)long 和 int 可以互轉嗎 ？

可以的，Java 中的 `long` 和 `int` 可以相互轉換。由於 `long` 類型的範圍比 `int` 類型大，因此將 `int` 轉換為 `long` 是安全的，而將 `long` 轉換為 `int` 可能會導致數據丢失或溢出。

將 `int` 轉換為 `long` 可以通過直接賦值或強制類型轉換來實現。例如：

```
int intValue = 10;
long longValue = intValue; // 自動轉換，安全的
```

將 `long` 轉換為 `int` 需要使用強制類型轉換，但需要注意潛在的數據丢失或溢出問題。

![[IMG-小林  Java 基礎面試題-20250807203911704.png]]

例如：

```
long longValue = 100L;
int intValue = (int) longValue; // 強制類型轉換，可能會有數據丢失或溢出
```

在將 `long` 轉換為 `int` 時，如果 `longValue` 的值超出了 `int` 類型的範圍，轉換結果將是截斷後的低位部分。因此，在進行轉換之前，建議先檢查 `longValue` 的值是否在 `int` 類型的範圍内，以避免數據丢失或溢出的問題。

### [#](https://xiaolincoding.com/interview/java.html#%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E8%BD%AC%E6%8D%A2%E6%96%B9%E5%BC%8F%E4%BD%A0%E7%9F%A5%E9%81%93%E5%93%AA%E4%BA%9B)數據類型轉換方式你知道哪些？

- 自動類型轉換（隐式轉換）：當目標類型的範圍大於源類型時，Java 會自動將源類型轉換為目標類型，不需要顯式的類型轉換。例如，將 `int` 轉換為 `long`、將 `float` 轉換為 `double` 等。
- 強制類型轉換（顯式轉換）：當目標類型的範圍小於源類型時，需要使用強制類型轉換將源類型轉換為目標類型。這可能導致數據丢失或溢出。例如，將 `long` 轉換為 `int`、將 `double` 轉換為 `int` 等。語法為：目標類型 變量名 = (目標類型) 源類型。
- 字符串轉換：Java 提供了將字符串表示的數據轉換為其他類型數據的方法。例如，將字符串轉換為整型 `int`，可以使用 `Integer.parseInt()` 方法；將字符串轉換為浮點型 `double`，可以使用 `Double.parseDouble()` 方法等。
- 數值之間的轉換：Java 提供了一些數值類型之間的轉換方法，如將整型轉換為字符型、將字符型轉換為整型等。這些轉換方式可以通過類型的包裝類來實現，例如 `Character` 類、`Integer` 類等提供了相應的轉換方法。

### [#](https://xiaolincoding.com/interview/java.html#%E7%B1%BB%E5%9E%8B%E4%BA%92%E8%BD%AC%E4%BC%9A%E5%87%BA%E7%8E%B0%E4%BB%80%E4%B9%88%E9%97%AE%E9%A2%98%E5%90%97)類型互轉會出現什麽問題嗎？

- 數據丢失：當將一個範圍較大的數據類型轉換為一個範圍較小的數據類型時，可能會發生數據丢失。例如，將一個 `long` 類型的值轉換為 `int` 類型時，如果 `long` 值超出了 `int` 類型的範圍，轉換結果將是截斷後的低位部分，高位部分的數據將丢失。
- 數據溢出：與數據丢失相反，當將一個範圍較小的數據類型轉換為一個範圍較大的數據類型時，可能會發生數據溢出。例如，將一個 `int` 類型的值轉換為 `long` 類型時，轉換結果會填充額外的高位空間，但原始數據仍然保持不變。
- 精度損失：在進行浮點數類型的轉換時，可能會發生精度損失。由於浮點數的表示方式不同，將一個單精度浮點數 (`float`) 轉換為雙精度浮點數 (`double`) 時，精度可能會損失。
- 類型不匹配導致的錯誤：在進行類型轉換時，需要确保源類型和目標類型是兼容的。如果兩者不兼容，會導致編譯錯誤或運行時錯誤。

### [#](https://xiaolincoding.com/interview/java.html#%E4%B8%BA%E4%BB%80%E4%B9%88%E7%94%A8bigdecimal-%E4%B8%8D%E7%94%A8double)為什麽用 bigDecimal 不用 double ？

double 會出現精度丢失的問題，double 執行的是二進制浮點運算，二進制有些情況下不能準确的表示一個小數，就像十進制不能準确的表示 1/3 (1/3=0.3333...)，也就是說二進制表示小數的時候隻能夠表示能夠用 1/(2^n) 的和的任意組合，但是 0.1 不能夠精确表示，因為它不能夠表示成為 1/(2^n) 的和的形式。

比如：

```
System.out.println(0.05 + 0.01);
System.out.println(1.0 - 0.42);
System.out.println(4.015 * 100);
System.out.println(123.3 / 100);

輸出：
0.060000000000000005
0.5800000000000001
401.49999999999994
1.2329999999999999
```

可以看到在 Java 中進行浮點數運算的時候，會出現丢失精度的問題。那麽我們如果在進行商品價格計算的時候，就會出現問題。很有可能造成我們手中有 0.06 元，卻無法購買一個 0.05 元和一個 0.01 元的商品。因為如上所示，他們兩個的總和為 0.060000000000000005。這無疑是一個很嚴重的問題，尤其是當電商網站的并發量上去的時候，出現的問題將是巨大的。可能會導致無法下單，或者對賬出現問題。

而 Decimal 是精确計算，所以一般牽扯到金錢的計算，都使用 Decimal。

```
import java.math.BigDecimal;

public class BigDecimalExample {
    public static void main(String[] args) {
        BigDecimal num1 = new BigDecimal("0.1");
        BigDecimal num2 = new BigDecimal("0.2");

        BigDecimal sum = num1.add(num2);
        BigDecimal product = num1.multiply(num2);

        System.out.println("Sum: " + sum);
        System.out.println("Product: " + product);
    }
}

//輸出
Sum: 0.3
Product: 0.02
```

在上述代碼中，我們創建了兩個 `BigDecimal` 對象 `num1` 和 `num2`，分别表示 0.1 和 0.2 這兩個十進制數。然後，我們使用 `add()` 方法計算它們的和，并使用 `multiply()` 方法計算它們的乘積。最後，我們通過 `System.out.println()` 打印結果。

這樣的使用 `BigDecimal` 可以确保精确的十進制數值計算，避免了使用 `double` 可能出現的舍入誤差。需要注意的是，在創建 `BigDecimal` 對象時，應該使用字符串作為參數，而不是直接使用浮點數值，以避免浮點數精度丢失。

### [#](https://xiaolincoding.com/interview/java.html#%E8%A3%85%E7%AE%B1%E5%92%8C%E6%8B%86%E7%AE%B1%E6%98%AF%E4%BB%80%E4%B9%88)裝箱和拆箱是什麽？

裝箱（Boxing）和拆箱（Unboxing）是將基本數據類型和對應的包裝類之間進行轉換的過程。

```
Integer i = 10;  //裝箱
int n = i;   //拆箱
```

自動裝箱主要發生在兩種情況，一種是賦值時，另一種是在方法調用的時候。

> 賦值時

這是最常見的一種情況，在 Java 1.5 以前我們需要手動地進行轉換才行，而現在所有的轉換都是由編譯器來完成。

```
//before autoboxing
Integer iObject = Integer.valueOf(3);
Int iPrimitive = iObject.intValue()

//after java5
Integer iObject = 3; //autobxing - primitive to wrapper conversion
int iPrimitive = iObject; //unboxing - object to primitive conversion
```

> 方法調用時

當我們在方法調用時，我們可以傳入原始數據值或者對象，同樣編譯器會幫我們進行轉換。

```
public static Integer show(Integer iParam){
   System.out.println("autoboxing example - method invocation i: " + iParam);
   return iParam;
}

//autoboxing and unboxing in method invocation
show(3); //autoboxing
int result = show(3); //unboxing because return type of method is Integer
```

show 方法接受 Integer 對象作為參數，當調用 `show(3)` 時，會將 int 值轉換成對應的 Integer 對象，這就是所謂的自動裝箱，show 方法返回 Integer 對象，而 `int result = show(3);` 中 result 為 int 類型，所以這時候發生自動拆箱操作，將 show 方法的返回的 Integer 對象轉換成 int 值。

> 自動裝箱的弊端

自動裝箱有一個問題，那就是在一個循環中進行自動裝箱操作的情況，如下面的例子就會創建多餘的對象，影響程序的性能。

```
Integer sum = 0; for(int i=1000; i<5000; i++){   sum+=i; } 
```

上面的代碼 `sum+=i` 可以看成 `sum = sum + i`，但是 `+` 這個操作符不适用於 Integer 對象，首先 sum 進行自動拆箱操作，進行數值相加操作，最後發生自動裝箱操作轉換成 Integer 對象。其内部變化如下

```
int result = sum.intValue() + i; Integer sum = new Integer(result); 
```

由於我們這裏聲明的 sum 為 Integer 類型，在上面的循環中會創建將近 4000 個無用的 Integer 對象，在這樣龐大的循環中，會降低程序的性能并且加重了垃圾回收的工作量。因此在我們編程時，需要注意到這一點，正确地聲明變量類型，避免因為自動裝箱引起的性能問題。

### [#](https://xiaolincoding.com/interview/java.html#java%E4%B8%BA%E4%BB%80%E4%B9%88%E8%A6%81%E6%9C%89integer)Java 為什麽要有 Integer？

Integer 對應是 int 類型的包裝類，就是把 int 類型包裝成 Object 對象，對象封裝有很多好處，可以把屬性也就是數據跟處理這些數據的方法結合在一起，比如 Integer 就有 parseInt () 等方法來專門處理 int 型相關的數據。

另一個非常重要的原因就是在 Java 中絕大部分方法或類都是用來處理類類型對象的，如 ArrayList 集合類就隻能以類作為他的存儲對象，而這時如果想把一個 int 型的數據存入 list 是不可能的，必須把它包裝成類，也就是 Integer 才能被 List 所接受。所以 Integer 的存在是很必要的。

> 泛型中的應用

在 Java 中，泛型隻能使用引用類型，而不能使用基本類型。因此，如果要在泛型中使用 int 類型，必須使用 Integer 包裝類。例如，假設我們有一個列表，我們想要將其元素排序，并將排序結果存儲在一個新的列表中。如果我們使用基本數據類型 int，無法直接使用 Collections.sort () 方法。但是，如果我們使用 Integer 包裝類，我們就可以輕松地使用 Collections.sort () 方法。

```
List<Integer> list = new ArrayList<>();
list.add(3);
list.add(1);
list.add(2);
Collections.sort(list);
System.out.println(list);
```

> 轉換中的應用

在 Java 中，基本類型和引用類型不能直接進行轉換，必須使用包裝類來實現。例如，將一個 int 類型的值轉換為 String 類型，必須首先將其轉換為 Integer 類型，然後再轉換為 String 類型。

```
int i = 10;
Integer integer = new Integer(i);
String str = integer.toString();
System.out.println(str);
```

> 集合中的應用

Java 集合中隻能存儲對象，而不能存儲基本數據類型。因此，如果要將 int 類型的數據存儲在集合中，必須使用 Integer 包裝類。例如，假設我們有一個列表，我們想要計算列表中所有元素的和。如果我們使用基本數據類型 int，我們需要使用一個循環來遍曆列表，并將每個元素相加。但是，如果我們使用 Integer 包裝類，我們可以直接使用 stream () 方法來計算所有元素的和。

```
List<Integer> list = new ArrayList<>();
list.add(3);
list.add(1);
list.add(2);
int sum = list.stream().mapToInt(Integer::intValue).sum();
System.out.println(sum);
```

### [#](https://xiaolincoding.com/interview/java.html#integer%E7%9B%B8%E6%AF%94int%E6%9C%89%E4%BB%80%E4%B9%88%E4%BC%98%E7%82%B9)Integer 相比 int 有什麽優點？

int 是 Java 中的原始數據類型，而 Integer 是 int 的包裝類。

Integer 和 int 的區别：

- 基本類型和引用類型：首先，int 是一種基本數據類型，而 Integer 是一種引用類型。基本數據類型是 Java 中最基本的數據類型，它們是預定義的，不需要實例化就可以使用。而引用類型則需要通過實例化對象來使用。這意味着，使用 int 來存儲一個整數時，不需要任何額外的内存分配，而使用 Integer 時，必須為對象分配内存。在性能方面，基本數據類型的操作通常比相應的引用類型快。
- 自動裝箱和拆箱：其次，Integer 作為 int 的包裝類，它可以實現自動裝箱和拆箱。自動裝箱是指將基本類型轉化為相應的包裝類類型，而自動拆箱則是將包裝類類型轉化為相應的基本類型。這使得 Java 程序員更加方便地進行數據類型轉換。例如，當我們需要將 int 類型的值賦給 Integer 變量時，Java 可以自動地將 int 類型轉換為 Integer 類型。同樣地，當我們需要將 Integer 類型的值賦給 int 變量時，Java 可以自動地將 Integer 類型轉換為 int 類型。
- 空指針異常：另外，int 變量可以直接賦值為 0，而 Integer 變量必須通過實例化對象來賦值。如果對一個未經初始化的 Integer 變量進行操作，就會出現空指針異常。這是因為它被賦予了 null 值，而 null 值是無法進行自動拆箱的。

### [#](https://xiaolincoding.com/interview/java.html#%E9%82%A3%E4%B8%BA%E4%BB%80%E4%B9%88%E8%BF%98%E8%A6%81%E4%BF%9D%E7%95%99int%E7%B1%BB%E5%9E%8B)那為什麽還要保留 int 類型？

包裝類是引用類型，對象的引用和對象本身是分開存儲的，而對於基本類型數據，變量對應的内存塊直接存儲數據本身。

因此，基本類型數據在讀寫效率方面，要比包裝類高效。除此之外，在 64 位 JVM 上，在開啓引用壓縮的情況下，一個 Integer 對象占用 16 個字節的内存空間，而一個 int 類型數據隻占用 4 字節的内存空間，前者對空間的占用是後者的 4 倍。

也就是說，不管是讀寫效率，還是存儲效率，基本類型都比包裝類高效。

### [#](https://xiaolincoding.com/interview/java.html#%E8%AF%B4%E4%B8%80%E4%B8%8B-integer%E7%9A%84%E7%BC%93%E5%AD%98)說一下 integer 的緩存

Java 的 Integer 類内部實現了一個靜态緩存池，用於存儲特定範圍内的整數值對應的 Integer 對象。

默認情況下，這個範圍是 - 128 至 127。當通過 Integer.valueOf (int) 方法創建一個在這個範圍内的整數對象時，并不會每次都生成新的對象實例，而是複用緩存中的現有對象，會直接從内存中取出，不需要新建一個對象。

## [#](https://xiaolincoding.com/interview/java.html#%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1)面向對象

### [#](https://xiaolincoding.com/interview/java.html#%E6%80%8E%E4%B9%88%E7%90%86%E8%A7%A3%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1-%E7%AE%80%E5%8D%95%E8%AF%B4%E8%AF%B4%E5%B0%81%E8%A3%85%E7%BB%A7%E6%89%BF%E5%A4%9A%E6%80%81)怎麽理解面向對象？簡單說說封裝繼承多态

面向對象是一種編程範式，它**將現實世界中的事物抽象為對象**，對象具有屬性（稱為字段或屬性）和行為（稱為方法）。面向對象編程的設計思想是以對象為中心，通過對象之間的交互來完成程序的功能，具有靈活性和可擴展性，通過封裝和繼承可以更好地應對需求變化。

Java 面向對象的三大特性包括：**封裝、繼承、多态**：

- **封裝**：封裝是指將對象的屬性（數據）和行為（方法）結合在一起，對外隐藏對象的内部細節，僅通過對象提供的接口與外界交互。封裝的目的是增強安全性和簡化編程，使得對象更加獨立。
- **繼承**：繼承是一種可以使得子類自動共享父類數據結構和方法的機制。它是代碼複用的重要手段，通過繼承可以建立類與類之間的層次關系，使得結構更加清晰。
- **多态**：多态是指允許不同類的對象對同一消息作出響應。即同一個接口，使用不同的實例而執行不同操作。多态性可以分為編譯時多态（重載）和運行時多态（重寫）。它使得程序具有良好的靈活性和擴展性。

### [#](https://xiaolincoding.com/interview/java.html#%E5%A4%9A%E6%80%81%E4%BD%93%E7%8E%B0%E5%9C%A8%E5%93%AA%E5%87%A0%E4%B8%AA%E6%96%B9%E9%9D%A2)多态體現在哪幾個方面？

多态在面向對象編程中可以體現在以下幾個方面：

- **方法重載：**
    - 方法重載是指同一類中可以有多個同名方法，它們具有不同的參數列表（參數類型、數量或順序不同）。雖然方法名相同，但根據傳入的參數不同，編譯器會在編譯時确定調用哪個方法。
    - 示例：對於一個 `add` 方法，可以定義為 `add(int a, int b)` 和 `add(double a, double b)`。
- **方法重寫：**
    - 方法重寫是指子類能夠提供對父類中同名方法的具體實現。在運行時，JVM 會根據對象的實際類型确定調用哪個版本的方法。這是實現多态的主要方式。
    - 示例：在一個動物類中，定義一個 `sound` 方法，子類 `Dog` 可以重寫該方法以實現 `bark`，而 `Cat` 可以實現 `meow`。
- **接口與實現：**
    - 多态也體現在接口的使用上，多個類可以實現同一個接口，并且用接口類型的引用來調用這些類的方法。這使得程序在面對不同具體實現時保持一貫的調用方式。
    - 示例：多個類（如 `Dog`, `Cat`）都實現了一個 `Animal` 接口，當用 `Animal` 類型的引用來調用 `makeSound` 方法時，會觸發對應的實現。
- **向上轉型和向下轉型：**
    - 在 Java 中，可以使用父類類型的引用指向子類對象，這是向上轉型。通過這種方式，可以在運行時期采用不同的子類實現。
    - 向下轉型是將父類引用轉回其子類類型，但在執行前需要确認引用實際指向的對象類型以避免 `ClassCastException`。

### [#](https://xiaolincoding.com/interview/java.html#%E5%A4%9A%E6%80%81%E8%A7%A3%E5%86%B3%E4%BA%86%E4%BB%80%E4%B9%88%E9%97%AE%E9%A2%98)多态解決了什麽問題？

多态是指子類可以替換父類，在實際的代碼運行過程中，調用子類的方法實現。多态這種特性也需要編程語言提供特殊的語法機制來實現，比如繼承、接口類。

多态可以提高代碼的擴展性和複用性，是很多設計模式、設計原則、編程技巧的代碼實現基礎。比如策略模式、基於接口而非實現編程、依賴倒置原則、裏式替換原則、利用多态去掉冗長的 if-else 語句等等

### [#](https://xiaolincoding.com/interview/java.html#%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E7%9A%84%E8%AE%BE%E8%AE%A1%E5%8E%9F%E5%88%99%E4%BD%A0%E7%9F%A5%E9%81%93%E6%9C%89%E5%93%AA%E4%BA%9B%E5%90%97)面向對象的設計原則你知道有哪些嗎

面向對象編程中的六大原則：

- **單一職責原則（SRP）**：一個類應該隻有一個引起它變化的原因，即一個類應該隻負責一項職責。例子：考慮一個員工類，它應該隻負責管理員工信息，而不應負責其他無關工作。
- **開放封閉原則（OCP）**：軟件實體應該對擴展開放，對修改封閉。例子：通過制定接口來實現這一原則，比如定義一個圖形類，然後讓不同類型的圖形繼承這個類，而不需要修改圖形類本身。
- **裏氏替換原則（LSP）**：子類對象應該能夠替換掉所有父類對象。例子：一個正方形是一個矩形，但如果修改一個矩形的高度和寬度時，正方形的行為應該如何改變就是一個違反裏氏替換原則的例子。
- **接口隔離原則（ISP）**：客戶端不應該依賴那些它不需要的接口，即接口應該小而專。例子：通過接口抽象層來實現底層和高層模塊之間的解耦，比如使用依賴注入。
- **依賴倒置原則（DIP）**：高層模塊不應該依賴低層模塊，二者都應該依賴於抽象；抽象不應該依賴於細節，細節應該依賴於抽象。例子：如果一個公司類包含部門類，應該考慮使用合成 / 聚合關系，而不是將公司類繼承自部門類。
- **最少知識原則 (Law of Demeter)**：一個對象應當對其他對象有最少的了解，隻與其直接的朋友交互。

### [#](https://xiaolincoding.com/interview/java.html#%E9%87%8D%E8%BD%BD%E4%B8%8E%E9%87%8D%E5%86%99%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB)重載與重寫有什麽區别？

- 重載（Overloading）指的是在同一個類中，可以有多個同名方法，它們具有不同的參數列表（參數類型、參數個數或參數順序不同），編譯器根據調用時的參數類型來決定調用哪個方法。
- 重寫（Overriding）指的是子類可以重新定義父類中的方法，方法名、參數列表和返回類型必須與父類中的方法一致，通過 @override 注解來明确表示這是對父類方法的重寫。

重載是指在同一個類中定義多個同名方法，而重寫是指子類重新定義父類中的方法。

### [#](https://xiaolincoding.com/interview/java.html#%E6%8A%BD%E8%B1%A1%E7%B1%BB%E5%92%8C%E6%99%AE%E9%80%9A%E7%B1%BB%E5%8C%BA%E5%88%AB)抽象類和普通類區别？

- 實例化：普通類可以直接實例化對象，而抽象類不能被實例化，隻能被繼承。
- 方法實現：普通類中的方法可以有具體的實現，而抽象類中的方法可以有實現也可以沒有實現。
- 繼承：一個類可以繼承一個普通類，而且可以繼承多個接口；而一個類隻能繼承一個抽象類，但可以同時實現多個接口。
- 實現限制：普通類可以被其他類繼承和使用，而抽象類一般用於作為基類，被其他類繼承和擴展使用。

### [#](https://xiaolincoding.com/interview/java.html#java%E6%8A%BD%E8%B1%A1%E7%B1%BB%E5%92%8C%E6%8E%A5%E5%8F%A3%E7%9A%84%E5%8C%BA%E5%88%AB%E6%98%AF%E4%BB%80%E4%B9%88)Java 抽象類和接口的區别是什麽？

**兩者的特點：**

- 抽象類用於描述類的共同特性和行為，可以有成員變量、構造方法和具體方法。适用於有明顯繼承關系的場景。
- 接口用於定義行為規範，可以多實現，隻能有常量和抽象方法（Java 8 以後可以有默認方法和靜态方法）。适用於定義類的能力或功能。

**兩者的區别：**

- 實現方式：實現接口的關鍵字為 implements，繼承抽象類的關鍵字為 extends。一個類可以實現多個接口，但一個類隻能繼承一個抽象類。所以，使用接口可以間接地實現多重繼承。
- 方法方式：接口隻有定義，不能有方法的實現，java 1.8 中可以定義 default 方法體，而抽象類可以有定義與實現，方法可在抽象類中實現。
- 訪問修飾符：接口成員變量默認為 public static final，必須賦初值，不能被修改；其所有的成員方法都是 public、abstract 的。抽象類中成員變量默認 default，可在子類中被重新定義，也可被重新賦值；抽象方法被 abstract 修飾，不能被 private、static、synchronized 和 native 等修飾，必須以分號結尾，不帶花括號。
- 變量：抽象類可以包含實例變量和靜态變量，而接口隻能包含常量（即靜态常量）。

### [#](https://xiaolincoding.com/interview/java.html#%E6%8A%BD%E8%B1%A1%E7%B1%BB%E8%83%BD%E5%8A%A0final%E4%BF%AE%E9%A5%B0%E5%90%97)抽象類能加 final 修飾嗎？

**不能**，Java 中的抽象類是用來被繼承的，而 final 修飾符用於禁止類被繼承或方法被重寫，因此，抽象類和 final 修飾符是互斥的，不能同時使用。

### [#](https://xiaolincoding.com/interview/java.html#%E6%8E%A5%E5%8F%A3%E9%87%8C%E9%9D%A2%E5%8F%AF%E4%BB%A5%E5%AE%9A%E4%B9%89%E5%93%AA%E4%BA%9B%E6%96%B9%E6%B3%95)接口裏面可以定義哪些方法？

- **抽象方法**

抽象方法是接口的核心部分，所有實現接口的類都必須實現這些方法。抽象方法默認是 public 和 abstract，這些修飾符可以省略。

```
public interface Animal {
    void makeSound();
}
```

- **默認方法**

默認方法是在 Java 8 中引入的，允許接口提供具體實現。實現類可以選擇重寫默認方法。

```
public interface Animal {
    void makeSound();
    
    default void sleep() {
        System.out.println("Sleeping...");
    }
}
```

- **靜态方法**

靜态方法也是在 Java 8 中引入的，它們屬於接口本身，可以通過接口名直接調用，而不需要實現類的對象。

```
public interface Animal {
    void makeSound();
    
    static void staticMethod() {
        System.out.println("Static method in interface");
    }
}
```

- **私有方法**

私有方法是在 Java 9 中引入的，用於在接口中為默認方法或其他私有方法提供輔助功能。這些方法不能被實現類訪問，隻能在接口内部使用。

```
public interface Animal {
    void makeSound();
    
    default void sleep() {
        System.out.println("Sleeping...");
        logSleep();
    }
    
    private void logSleep() {
        System.out.println("Logging sleep");
    }
}
```

```
public interface Animal {
    void makeSound();
}
```

### [#](https://xiaolincoding.com/interview/java.html#%E6%8A%BD%E8%B1%A1%E7%B1%BB%E5%8F%AF%E4%BB%A5%E8%A2%AB%E5%AE%9E%E4%BE%8B%E5%8C%96%E5%90%97)抽象類可以被實例化嗎？

在 Java 中，抽象類本身不能被實例化。

這意味着不能使用 `new` 關鍵字直接創建一個抽象類的對象。抽象類的存在主要是為了被繼承，它通常包含一個或多個抽象方法（由 `abstract` 關鍵字修飾且無方法體的方法），這些方法需要在子類中被實現。

抽象類可以有構造器，這些構造器在子類實例化時會被調用，以便進行必要的初始化工作。然而，這個過程并不是直接實例化抽象類，而是創建了子類的實例，間接地使用了抽象類的構造器。

例如：

```
public abstract class AbstractClass {
    public AbstractClass() {
        // 構造器代碼
    }
    
    public abstract void abstractMethod();
}

public class ConcreteClass extends AbstractClass {
    public ConcreteClass() {
        super(); // 調用抽象類的構造器
    }
    
    @Override
    public void abstractMethod() {
        // 實現抽象方法
    }
}

// 下面的代碼可以運行
ConcreteClass obj = new ConcreteClass();
```

在這個例子中，`ConcreteClass` 繼承了 `AbstractClass` 并實現了抽象方法 `abstractMethod()`。當我們創建 `ConcreteClass` 的實例時，`AbstractClass` 的構造器被調用，但這并不意味着 `AbstractClass` 被實例化；實際上，我們創建的是 `ConcreteClass` 的一個對象。

簡而言之，抽象類不能直接實例化，但通過繼承抽象類并實現所有抽象方法的子類是可以被實例化的。

### [#](https://xiaolincoding.com/interview/java.html#%E6%8E%A5%E5%8F%A3%E5%8F%AF%E4%BB%A5%E5%8C%85%E5%90%AB%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0%E5%90%97)接口可以包含構造函數嗎？

在接口中，不可以有構造方法，在接口裏寫入構造方法時，編譯器提示：Interfaces cannot have constructors，因為接口不會有自己的實例的，所以不需要有構造函數。

為什麽呢？構造函數就是初始化 class 的屬性或者方法，在 new 的一瞬間自動調用，那麽問題來了 Java 的接口，都不能 new 那麽要構造函數幹嘛呢？根本就沒法調用

### [#](https://xiaolincoding.com/interview/java.html#%E8%A7%A3%E9%87%8Ajava%E4%B8%AD%E7%9A%84%E9%9D%99%E6%80%81%E5%8F%98%E9%87%8F%E5%92%8C%E9%9D%99%E6%80%81%E6%96%B9%E6%B3%95)解釋 Java 中的靜态變量和靜态方法

在 Java 中，靜态變量和靜态方法是與類本身關聯的，而不是與類的實例（對象）關聯。它們在内存中隻存在一份，可以被類的所有實例共享。

> 靜态變量

靜态變量（也稱為類變量）是在類中使用 `static` 關鍵字聲明的變量。它們屬於類而不是任何具體的對象。主要的特點：

- **共享性**：所有該類的實例共享同一個靜态變量。如果一個實例修改了靜态變量的值，其他實例也會看到這個更改。
- **初始化**：靜态變量在類被加載時初始化，隻會對其進行一次分配内存。
- **訪問方式**：靜态變量可以直接通過類名訪問，也可以通過實例訪問，但推薦使用類名。

示例：

```
public class MyClass {
    static int staticVar = 0; // 靜态變量

    public MyClass() {
        staticVar++; // 每創建一個對象，靜态變量自增
    }
    
    public static void printStaticVar() {
        System.out.println("Static Var: " + staticVar);
    }
}

// 使用示例
MyClass obj1 = new MyClass();
MyClass obj2 = new MyClass();
MyClass.printStaticVar(); // 輸出 Static Var: 2
```

> 靜态方法

靜态方法是在類中使用 `static` 關鍵字聲明的方法。類似於靜态變量，靜态方法也屬於類，而不是任何具體的對象。主要的特點：

- **無實例依賴**：靜态方法可以在沒有創建類實例的情況下調用。對於靜态方法來說，不能直接訪問非靜态的成員變量或方法，因為靜态方法沒有上下文的實例。
- **訪問靜态成員**：靜态方法可以直接調用其他靜态變量和靜态方法，但不能直接訪問非靜态成員。
- **多态性**：靜态方法不支持重寫（Override），但可以被隐藏（Hide）。

```
public class MyClass {
    static int count = 0;

    // 靜态方法
    public static void incrementCount() {
        count++;
    }

    public static void displayCount() {
        System.out.println("Count: " + count);
    }
}

// 使用示例
MyClass.incrementCount(); // 調用靜态方法
MyClass.displayCount();   // 輸出 Count: 1
```

> 使用場景

- **靜态變量**：常用於需要在所有對象間共享的數據，如計數器、常量等。
- **靜态方法**：常用於助手方法（utility methods）、獲取類級别的信息或者是沒有依賴於實例的數據處理。

### [#](https://xiaolincoding.com/interview/java.html#%E9%9D%9E%E9%9D%99%E6%80%81%E5%86%85%E9%83%A8%E7%B1%BB%E5%92%8C%E9%9D%99%E6%80%81%E5%86%85%E9%83%A8%E7%B1%BB%E7%9A%84%E5%8C%BA%E5%88%AB)非靜态内部類和靜态内部類的區别？

區别包括：

- 非靜态内部類依賴於外部類的實例，而靜态内部類不依賴於外部類的實例。
- 非靜态内部類可以訪問外部類的實例變量和方法，而靜态内部類隻能訪問外部類的靜态成員。
- 非靜态内部類不能定義靜态成員，而靜态内部類可以定義靜态成員。
- 非靜态内部類在外部類實例化後才能實例化，而靜态内部類可以獨立實例化。
- 非靜态内部類可以訪問外部類的私有成員，而靜态内部類不能直接訪問外部類的私有成員，需要通過實例化外部類來訪問。

### [#](https://xiaolincoding.com/interview/java.html#%E9%9D%9E%E9%9D%99%E6%80%81%E5%86%85%E9%83%A8%E7%B1%BB%E5%8F%AF%E4%BB%A5%E7%9B%B4%E6%8E%A5%E8%AE%BF%E9%97%AE%E5%A4%96%E9%83%A8%E6%96%B9%E6%B3%95-%E7%BC%96%E8%AF%91%E5%99%A8%E6%98%AF%E6%80%8E%E4%B9%88%E5%81%9A%E5%88%B0%E7%9A%84)非靜态内部類可以直接訪問外部方法，編譯器是怎麽做到的？

非靜态内部類可以直接訪問外部方法是因為編譯器在生成字節碼時會為非靜态内部類維護一個指向外部類實例的引用。

這個引用使得非靜态内部類能夠訪問外部類的實例變量和方法。編譯器會在生成非靜态内部類的構造方法時，將外部類實例作為參數傳入，并在内部類的實例化過程中建立外部類實例與内部類實例之間的聯系，從而實現直接訪問外部方法的功能。

### [#](https://xiaolincoding.com/interview/java.html#%E6%9C%89%E4%B8%80%E4%B8%AA%E7%88%B6%E7%B1%BB%E5%92%8C%E5%AD%90%E7%B1%BB-%E9%83%BD%E6%9C%89%E9%9D%99%E6%80%81%E7%9A%84%E6%88%90%E5%91%98%E5%8F%98%E9%87%8F%E3%80%81%E9%9D%99%E6%80%81%E6%9E%84%E9%80%A0%E6%96%B9%E6%B3%95%E5%92%8C%E9%9D%99%E6%80%81%E6%96%B9%E6%B3%95-%E5%9C%A8%E6%88%91new%E4%B8%80%E4%B8%AA%E5%AD%90%E7%B1%BB%E5%AF%B9%E8%B1%A1%E7%9A%84%E6%97%B6%E5%80%99-%E5%8A%A0%E8%BD%BD%E9%A1%BA%E5%BA%8F%E6%98%AF%E6%80%8E%E4%B9%88%E6%A0%B7%E7%9A%84)有一個父類和子類，都有靜态的成員變量、靜态構造方法和靜态方法，在我 new 一個子類對象的時候，加載順序是怎麽樣的？

當你實例化一個子類對象時，靜态成員變量、靜态構造方法和靜态方法的加載順序遵循以下步驟：

- 在創建子類對象之前，首先會加載父類的靜态成員變量和靜态代碼塊（構造方法無法被 `static` 修飾，因此這裏是靜态代碼塊）。這個加載是在類首次被加載時進行的，且隻會發生一次。
- 接下來，加載子類的靜态成員變量和靜态代碼塊。這一過程也隻發生一次，即當首次使用子類的相關代碼時。
- 之後，執行實例化子類對象的過程。這時會呼叫父類構造方法，然後是子類的構造方法。

具體加載順序可以簡要總結為：

- **父類靜态成員變量、靜态代碼塊**（如果有）
- **子類靜态成員變量、靜态代碼塊**（如果有）
- **父類構造方法**（實例化對象時）
- **子類構造方法**（實例化對象時）

示例代碼

```
class Parent {
    static {
        System.out.println("Parent static block");
    }
    static int parentStaticVar = 10;

    Parent() {
        System.out.println("Parent constructor");
    }
}

class Child extends Parent {
    static {
        System.out.println("Child static block");
    }
    static int childStaticVar = 20;

    Child() {
        System.out.println("Child constructor");
    }
}

public class Main {
    public static void main(String[] args) {
        Child c = new Child();
    }
}
```

輸出結果

```
Parent static block
Child static block
Parent constructor
Child constructor
```

從輸出可以看出，在創建 `Child` 類型對象時，首先執行父類的靜态塊，然後是子類的靜态塊，最後才是父類和子類的構造函數。這清晰地展示了加載的順序。

## [#](https://xiaolincoding.com/interview/java.html#%E6%B7%B1%E6%8B%B7%E8%B4%9D%E5%92%8C%E6%B5%85%E6%8B%B7%E8%B4%9D)深拷貝和淺拷貝

### [#](https://xiaolincoding.com/interview/java.html#%E6%B7%B1%E6%8B%B7%E8%B4%9D%E5%92%8C%E6%B5%85%E6%8B%B7%E8%B4%9D%E7%9A%84%E5%8C%BA%E5%88%AB)深拷貝和淺拷貝的區别？

![[IMG-小林  Java 基礎面試題-20250807203913493.png]]

- 淺拷貝是指隻複制對象本身和其内部的值類型字段，但不會複制對象内部的引用類型字段。換句話說，淺拷貝隻是創建一個新的對象，然後將原對象的字段值複制到新對象中，但如果原對象内部有引用類型的字段，隻是將引用複制到新對象中，兩個對象指向的是同一個引用對象。
- 深拷貝是指在複制對象的同時，將對象内部的所有引用類型字段的内容也複制一份，而不是共享引用。換句話說，深拷貝會遞歸複制對象内部所有引用類型的字段，生成一個全新的對象以及其内部的所有對象。

### [#](https://xiaolincoding.com/interview/java.html#%E5%AE%9E%E7%8E%B0%E6%B7%B1%E6%8B%B7%E8%B4%9D%E7%9A%84%E4%B8%89%E7%A7%8D%E6%96%B9%E6%B3%95%E6%98%AF%E4%BB%80%E4%B9%88)實現深拷貝的三種方法是什麽？

在 Java 中，實現對象深拷貝的方法有以下幾種主要方式：

> 實現 Cloneable 接口并重寫 clone () 方法

這種方法要求對象及其所有引用類型字段都實現 Cloneable 接口，并且重寫 clone () 方法。在 clone () 方法中，通過遞歸克隆引用類型字段來實現深拷貝。

```
class MyClass implements Cloneable {
    private String field1;
    private NestedClass nestedObject;

    @Override
    protected Object clone() throws CloneNotSupportedException {
        MyClass cloned = (MyClass) super.clone();
        cloned.nestedObject = (NestedClass) nestedObject.clone(); // 深拷貝内部的引用對象
        return cloned;
    }
}

class NestedClass implements Cloneable {
    private int nestedField;

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

> 使用序列化和反序列化

通過將對象序列化為字節流，再從字節流反序列化為對象來實現深拷貝。要求對象及其所有引用類型字段都實現 Serializable 接口。

```
import java.io.*;

class MyClass implements Serializable {
    private String field1;
    private NestedClass nestedObject;

    public MyClass deepCopy() {
        try {
            ByteArrayOutputStream bos = new ByteArrayOutputStream();
            ObjectOutputStream oos = new ObjectOutputStream(bos);
            oos.writeObject(this);
            oos.flush();
            oos.close();

            ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
            ObjectInputStream ois = new ObjectInputStream(bis);
            return (MyClass) ois.readObject();
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
            return null;
        }
    }
}

class NestedClass implements Serializable {
    private int nestedField;
}
```

> 手動遞歸複制

針對特定對象結構，手動遞歸複制對象及其引用類型字段。适用於對象結構複雜度不高的情況。

```
class MyClass {
    private String field1;
    private NestedClass nestedObject;

    public MyClass deepCopy() {
        MyClass copy = new MyClass();
        copy.setField1(this.field1);
        copy.setNestedObject(this.nestedObject.deepCopy());
        return copy;
    }
}

class NestedClass {
    private int nestedField;

    public NestedClass deepCopy() {
        NestedClass copy = new NestedClass();
        copy.setNestedField(this.nestedField);
        return copy;
    }
}
```

## [#](https://xiaolincoding.com/interview/java.html#%E6%B3%9B%E5%9E%8B)泛型

### [#](https://xiaolincoding.com/interview/java.html#%E4%BB%80%E4%B9%88%E6%98%AF%E6%B3%9B%E5%9E%8B)什麽是泛型？

泛型是 Java 編程語言中的一個重要特性，它允許類、接口和方法在定義時使用一個或多個類型參數，這些類型參數在使用時可以被指定為具體的類型。

泛型的主要目的是在編譯時提供更強的類型檢查，并且在編譯後能夠保留類型信息，避免了在運行時出現類型轉換異常。

> 為什麽需要泛型？

- **适用於多種數據類型執行相同的代碼**

```
private static int add(int a, int b) {
    System.out.println(a + "+" + b + "=" + (a + b));
    return a + b;
}

private static float add(float a, float b) {
    System.out.println(a + "+" + b + "=" + (a + b));
    return a + b;
}

private static double add(double a, double b) {
    System.out.println(a + "+" + b + "=" + (a + b));
    return a + b;
}
```

如果沒有泛型，要實現不同類型的加法，每種類型都需要重載一個 add 方法；通過泛型，我們可以複用為一個方法：

```
private static <T extends Number> double add(T a, T b) {
    System.out.println(a + "+" + b + "=" + (a.doubleValue() + b.doubleValue()));
    return a.doubleValue() + b.doubleValue();
}
```

- **泛型中的類型在使用時指定，不需要強制類型轉換**（**類型安全**，編譯器會**檢查類型**）

看下這個例子：

```
List list = new ArrayList();
list.add("xxString");
list.add(100d);
list.add(new Person());
```

我們在使用上述 list 中，list 中的元素都是 Object 類型（無法約束其中的類型），所以在取出集合元素時需要人為的強制類型轉化到具體的目標類型，且很容易出現 java.lang.ClassCastException 異常。

引入泛型，它將提供類型的約束，提供編譯前的檢查：

```
List<String> list = new ArrayList<String>();

// list中隻能放String, 不能放其它類型的元素
```

## [#](https://xiaolincoding.com/interview/java.html#%E5%AF%B9%E8%B1%A1)對象

### [#](https://xiaolincoding.com/interview/java.html#java%E5%88%9B%E5%BB%BA%E5%AF%B9%E8%B1%A1%E6%9C%89%E5%93%AA%E4%BA%9B%E6%96%B9%E5%BC%8F)java 創建對象有哪些方式？

在 Java 中，創建對象的方式有多種，常見的包括：

**使用 new 關鍵字**：通過 new 關鍵字直接調用類的構造方法來創建對象。

```
MyClass obj = new MyClass();
```

**使用 Class 類的 newInstance () 方法**：通過反射機制，可以使用 Class 類的 newInstance () 方法創建對象。

```
MyClass obj = (MyClass) Class.forName("com.example.MyClass").newInstance();
```

**使用 Constructor 類的 newInstance () 方法**：同樣是通過反射機制，可以使用 Constructor 類的 newInstance () 方法創建對象。

```
Constructor<MyClass> constructor = MyClass.class.getConstructor();
MyClass obj = constructor.newInstance();
```

**使用 clone () 方法**：如果類實現了 Cloneable 接口，可以使用 clone () 方法複制對象。

```
MyClass obj1 = new MyClass();
MyClass obj2 = (MyClass) obj1.clone();
```

**使用反序列化**：通過將對象序列化到文件或流中，然後再進行反序列化來創建對象。

```
// SerializedObject.java
ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("object.ser"));
out.writeObject(obj);
out.close();

// DeserializedObject.java
ObjectInputStream in = new ObjectInputStream(new FileInputStream("object.ser"));
MyClass obj = (MyClass) in.readObject();
in.close();
```

### [#](https://xiaolincoding.com/interview/java.html#java%E5%88%9B%E5%BB%BA%E5%AF%B9%E8%B1%A1%E9%99%A4%E4%BA%86new%E8%BF%98%E6%9C%89%E5%88%AB%E7%9A%84%E4%BB%80%E4%B9%88%E6%96%B9%E5%BC%8F)Java 創建對象除了 new 還有别的什麽方式？

- **通過反射創建對象**：通過 Java 的反射機制可以在運行時動态地創建對象。可以使用 Class 類的 newInstance () 方法或者通過 Constructor 類來創建對象。

```
public class MyClass {
    public MyClass() {
        // Constructor
    }
}

public class Main {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = MyClass.class;
        MyClass obj = (MyClass) clazz.newInstance();
    }
}
```

- **通過反序列化創建對象**：通過將對象序列化（保存到文件或網絡傳輸）然後再反序列化（從文件或網絡傳輸中讀取對象）的方式來創建對象，對象能被序列化和反序列化的前提是類實現 Serializable 接口。

```
import java.io.*;

public class MyClass implements Serializable {
    // Class definition
}

public class Main {
    public static void main(String[] args) throws Exception {
        // Serialize object
        MyClass obj = new MyClass();
        ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("object.ser"));
        out.writeObject(obj);
        out.close();
        
        // Deserialize object
        ObjectInputStream in = new ObjectInputStream(new FileInputStream("object.ser"));
        MyClass newObj = (MyClass) in.readObject();
        in.close();
    }
}
```

- **通過 clone 創建對象**：所有 Java 對象都繼承自 Object 類，Object 類中有一個 clone () 方法，可以用來創建對象的副本，要使用 clone 方法，我們必須先實現 Cloneable 接口并實現其定義的 clone 方法。

```
public class MyClass implements Cloneable {
    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}

public class Main {
    public static void main(String[] args) throws CloneNotSupportedException {
        MyClass obj1 = new MyClass();
        MyClass obj2 = (MyClass) obj1.clone();
    }
}
```

### [#](https://xiaolincoding.com/interview/java.html#new%E5%87%BA%E7%9A%84%E5%AF%B9%E8%B1%A1%E4%BB%80%E4%B9%88%E6%97%B6%E5%80%99%E5%9B%9E%E6%94%B6)New 出的對象什麽時候回收？

通過過關鍵字 `new` 創建的對象，由 Java 的垃圾回收器（Garbage Collector）負責回收。垃圾回收器的工作是在程序運行過程中自動進行的，它會周期性地檢測不再被引用的對象，并將其回收釋放内存。

具體來說，Java 對象的回收時機是由垃圾回收器根據一些算法來決定的，主要有以下幾種情況：

1. 引用計數法：某個對象的引用計數為 0 時，表示該對象不再被引用，可以被回收。
2. 可達性分析算法：從根對象（如方法區中的類靜态屬性、方法中的局部變量等）出發，通過對象之間的引用鏈進行遍曆，如果存在一條引用鏈到達某個對象，則說明該對象是可達的，反之不可達，不可達的對象將被回收。
3. 終結器（Finalizer）：如果對象重寫了 `finalize()` 方法，垃圾回收器會在回收該對象之前調用 `finalize()` 方法，對象可以在 `finalize()` 方法中進行一些清理操作。然而，終結器機制的使用不被推薦，因為它的執行時間是不确定的，可能會導致不可預測的性能問題。

## [#](https://xiaolincoding.com/interview/java.html#%E5%8F%8D%E5%B0%84)反射

### [#](https://xiaolincoding.com/interview/java.html#%E4%BB%80%E4%B9%88%E6%98%AF%E5%8F%8D%E5%B0%84)什麽是反射？

Java 反射機制是在運行狀态中，對於任意一個類，都能夠知道這個類中的所有屬性和方法，對於任意一個對象，都能夠調用它的任意一個方法和屬性；這種動态獲取的信息以及動态調用對象的方法的功能稱為 Java 語言的反射機制。

反射具有以下特性：

1. **運行時類信息訪問**：反射機制允許程序在運行時獲取類的完整結構信息，包括類名、包名、父類、實現的接口、構造函數、方法和字段等。
2. **動态對象創建**：可以使用反射 API 動态地創建對象實例，即使在編譯時不知道具體的類名。這是通過 Class 類的 newInstance () 方法或 Constructor 對象的 newInstance () 方法實現的。
3. **動态方法調用**：可以在運行時動态地調用對象的方法，包括私有方法。這通過 Method 類的 invoke () 方法實現，允許你傳入對象實例和參數值來執行方法。
4. **訪問和修改字段值**：反射還允許程序在運行時訪問和修改對象的字段值，即使是私有的。這是通過 Field 類的 get () 和 set () 方法完成的。

![[IMG-小林  Java 基礎面試題-20250807203915411.png]]


### [#](https://xiaolincoding.com/interview/java.html#%E5%8F%8D%E5%B0%84%E5%9C%A8%E4%BD%A0%E5%B9%B3%E6%97%B6%E5%86%99%E4%BB%A3%E7%A0%81%E6%88%96%E8%80%85%E6%A1%86%E6%9E%B6%E4%B8%AD%E7%9A%84%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF%E6%9C%89%E5%93%AA%E4%BA%9B)反射在你平時寫代碼或者框架中的應用場景有哪些？

> 加載數據庫驅動

我們的項目底層數據庫有時是用 mysql，有時用 oracle，需要動态地根據實際情況加載驅動類，這個時候反射就有用了，假設 com.mikechen.java.myqlConnection，com.mikechen.java.oracleConnection 這兩個類我們要用。

這時候我們在使用 JDBC 連接數據庫時使用 Class.forName () 通過反射加載數據庫的驅動程序，如果是 mysql 則傳入 mysql 的驅動類，而如果是 oracle 則傳入的參數就變成另一個了。

```
//  DriverManager.registerDriver(new com.mysql.cj.jdbc.Driver());
Class.forName("com.mysql.cj.jdbc.Driver");
```

> 配置文件加載

Spring 框架的 IOC（動态加載管理 Bean），Spring 通過配置文件配置各種各樣的 bean，你需要用到哪些 bean 就配哪些，spring 容器就會根據你的需求去動态加載，你的程序就能健壯地運行。

Spring 通過 XML 配置模式裝載 Bean 的過程：

- 將程序中所有 XML 或 properties 配置文件加載入内存
- Java 類裏面解析 xml 或者 properties 裏面的内容，得到對應實體類的字節碼字符串以及相關的屬性信息
- 使用反射機制，根據這個字符串獲得某個類的 Class 實例
- 動态配置實例的屬性

配置文件

```
className=com.example.reflectdemo.TestInvoke
methodName=printlnState
```

實體類

```
public class TestInvoke {
    private void printlnState(){
        System.out.println("I am fine");
    }
}
```

解析配置文件内容

```
// 解析xml或properties裏面的内容，得到對應實體類的字節碼字符串以及屬性信息
public static String getName(String key) throws IOException {
    Properties properties = new Properties();
    FileInputStream in = new FileInputStream("D:\IdeaProjects\AllDemos\language-specification\src\main\resources\application.properties");
    properties.load(in);
    in.close();
    return properties.getProperty(key);
}
```

利用反射獲取實體類的 Class 實例，創建實體類的實例對象，調用方法

```
public static void main(String[] args) throws NoSuchMethodException, InvocationTargetException, IllegalAccessException, IOException, ClassNotFoundException, InstantiationException {
    // 使用反射機制，根據這個字符串獲得Class對象
    Class<?> c = Class.forName(getName("className"));
    System.out.println(c.getSimpleName());
    // 獲取方法
    Method method = c.getDeclaredMethod(getName("methodName"));
    // 繞過安全檢查
    method.setAccessible(true);
    // 創建實例對象
    TestInvoke testInvoke = (TestInvoke)c.newInstance();
    // 調用方法
    method.invoke(testInvoke);

}
```

運行結果：
![[IMG-小林  Java 基礎面試題-20250807203917723.png]]


## [#](https://xiaolincoding.com/interview/java.html#%E6%B3%A8%E8%A7%A3)注解

### [#](https://xiaolincoding.com/interview/java.html#%E8%83%BD%E8%AE%B2%E4%B8%80%E8%AE%B2java%E6%B3%A8%E8%A7%A3%E7%9A%84%E5%8E%9F%E7%90%86%E5%90%97)能講一講 Java 注解的原理嗎？

注解本質是一個繼承了 Annotation 的特殊接口，其具體實現類是 Java 運行時生成的動态代理類。

我們通過反射獲取注解時，返回的是 Java 運行時生成的動态代理對象。通過代理對象調用自定義注解的方法，會最終調用 AnnotationInvocationHandler 的 invoke 方法。該方法會從 memberValues 這個 Map 中索引出對應的值。而 memberValues 的來源是 Java 常量池。

### [#](https://xiaolincoding.com/interview/java.html#java%E6%B3%A8%E8%A7%A3%E7%9A%84%E4%BD%9C%E7%94%A8%E5%9F%9F%E5%91%A2)Java 注解的作用域呢？

注解的作用域（Scope）指的是注解可以應用在哪些程序元素上，例如類、方法、字段等。Java 注解的作用域可以分為三種：

1. 類級别作用域：用於描述類的注解，通常放置在類定義的上面，可以用來指定類的一些屬性，如類的訪問級别、繼承關系、注釋等。
2. 方法級别作用域：用於描述方法的注解，通常放置在方法定義的上面，可以用來指定方法的一些屬性，如方法的訪問級别、返回值類型、異常類型、注釋等。
3. 字段級别作用域：用於描述字段的注解，通常放置在字段定義的上面，可以用來指定字段的一些屬性，如字段的訪問級别、默認值、注釋等。

除了這三種作用域，Java 還提供了其他一些注解作用域，例如構造函數作用域和局部變量作用域。這些注解作用域可以用來對構造函數和局部變量進行描述和注釋。

## [#](https://xiaolincoding.com/interview/java.html#%E5%BC%82%E5%B8%B8)異常

### [#](https://xiaolincoding.com/interview/java.html#%E4%BB%8B%E7%BB%8D%E4%B8%80%E4%B8%8Bjava%E5%BC%82%E5%B8%B8)介紹一下 Java 異常

Java 異常類層次結構圖：

![[IMG-小林  Java 基礎面試題-20250807203817207.png]]

1. **Error（錯誤）**：表示運行時環境的錯誤。錯誤是程序無法處理的嚴重問題，如系統崩潰、虛拟機錯誤、動态鏈接失敗等。通常，程序不應該嘗試捕獲這類錯誤。例如，OutOfMemoryError、StackOverflowError 等。
    
2. **Exception（異常）**：表示程序本身可以處理的異常條件。異常分為兩大類：
    
    - **非運行時異常**：這類異常在編譯時期就必須被捕獲或者聲明抛出。它們通常是外部錯誤，如文件不存在（FileNotFoundException）、類未找到（ClassNotFoundException）等。非運行時異常強制程序員處理這些可能出現的問題，增強了程序的健壯性。
        
    - **運行時異常**：這類異常包括運行時異常（RuntimeException）和錯誤（Error）。運行時異常由程序錯誤導致，如空指針訪問（NullPointerException）、數組越界（ArrayIndexOutOfBoundsException）等。運行時異常是不需要在編譯時強制捕獲或聲明的。
        

### [#](https://xiaolincoding.com/interview/java.html#java%E5%BC%82%E5%B8%B8%E5%A4%84%E7%90%86%E6%9C%89%E5%93%AA%E4%BA%9B)Java 異常處理有哪些？

異常處理是通過使用 try-catch 語句塊來捕獲和處理異常。以下是 Java 中常用的異常處理方式：

- try-catch 語句塊：用於捕獲并處理可能抛出的異常。try 塊中包含可能抛出異常的代碼，catch 塊用於捕獲并處理特定類型的異常。可以有多個 catch 塊來處理不同類型的異常。

```
try {
    // 可能抛出異常的代碼
} catch (ExceptionType1 e1) {
    // 處理異常類型1的邏輯
} catch (ExceptionType2 e2) {
    // 處理異常類型2的邏輯
} catch (ExceptionType3 e3) {
    // 處理異常類型3的邏輯
} finally {
    // 可選的finally塊，用於定義無論是否發生異常都會執行的代碼
}
```

- throw 語句：用於手動抛出異常。可以根據需要在代碼中使用 throw 語句主動抛出特定類型的異常。

```
throw new ExceptionType("Exception message");
```

- throws 關鍵字：用於在方法聲明中聲明可能抛出的異常類型。如果一個方法可能抛出異常，但不想在方法内部進行處理，可以使用 throws 關鍵字將異常傳遞給調用者來處理。

```
public void methodName() throws ExceptionType {
    // 方法體
}
```

- finally 塊：用於定義無論是否發生異常都會執行的代碼塊。通常用於釋放資源，确保資源的正确關閉。

```
try {
    // 可能抛出異常的代碼
} catch (ExceptionType e) {
    // 處理異常的邏輯
} finally {
    // 無論是否發生異常，都會執行的代碼
}
```

### [#](https://xiaolincoding.com/interview/java.html#%E6%8A%9B%E5%87%BA%E5%BC%82%E5%B8%B8%E4%B8%BA%E4%BB%80%E4%B9%88%E4%B8%8D%E7%94%A8throws)抛出異常為什麽不用 throws？

如果異常是未檢查異常或者在方法内部被捕獲和處理了，那麽就不需要使用 throws。

- **Unchecked Exceptions**：未檢查異常（unchecked exceptions）是繼承自 RuntimeException 類或 Error 類的異常，編譯器不強制要求進行異常處理。因此，對於這些異常，不需要在方法簽名中使用 throws 來聲明。示例包括 NullPointerException、ArrayIndexOutOfBoundsException 等。
- **捕獲和處理異常**：另一種常見情況是，在方法内部捕獲了可能抛出的異常，并在方法内部處理它們，而不是通過 throws 子句將它們傳遞到調用者。這種情況下，方法可以處理異常而無需在方法簽名中使用 throws。

### [#](https://xiaolincoding.com/interview/java.html#try-catch%E4%B8%AD%E7%9A%84%E8%AF%AD%E5%8F%A5%E8%BF%90%E8%A1%8C%E6%83%85%E5%86%B5)try catch 中的語句運行情況

try 塊中的代碼將按順序執行，如果抛出異常，將在 catch 塊中進行匹配和處理，然後程序將繼續執行 catch 塊之後的代碼。如果沒有匹配的 catch 塊，異常將被傳遞給上一層調用的方法。

### [#](https://xiaolincoding.com/interview/java.html#try-return-a-fianlly-return-b-%E8%BF%99%E6%9D%A1%E8%AF%AD%E5%8F%A5%E8%BF%94%E5%9B%9E%E5%95%A5)try {return “a”} fianlly {return “b”} 這條語句返回啥

finally 塊中的 return 語句會覆蓋 try 塊中的 return 返回，因此，該語句將返回 "b"。

## [#](https://xiaolincoding.com/interview/java.html#object)object

### [#](https://xiaolincoding.com/interview/java.html#%E4%B8%8E-equals-%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB)== 與 equals 有什麽區别？

對於字符串變量來說，使用 "==" 和 "equals" 比較字符串時，其比較方法不同。"==" 比較兩個變量本身的值，即兩個對象在内存中的首地址，"equals" 比較字符串包含内容是否相同。

對於非字符串變量來說，如果沒有對 equals () 進行重寫的話，"==" 和 "equals" 方法的作用是相同的，都是用來比較對象在堆内存中的首地址，即用來比較兩個引用變量是否指向同一個對象。

- ==：比較的是兩個字符串内存地址（堆内存）的數值是否相等，屬於數值比較；
- equals ()：比較的是兩個字符串的内容，屬於内容比較。

### [#](https://xiaolincoding.com/interview/java.html#stringbuffer%E5%92%8Cstringbuild%E5%8C%BA%E5%88%AB%E6%98%AF%E4%BB%80%E4%B9%88)StringBuffer 和 StringBuild 區别是什麽？

區别：

- String 是 Java 中基礎且重要的類，被聲明為 final class，是不可變字符串。因為它的不可變性，所以拼接字符串時候會産生很多無用的中間對象，如果頻繁的進行這樣的操作對性能有所影響。
- StringBuffer 就是為了解決大量拼接字符串時産生很多中間對象問題而提供的一個類。它提供了 append 和 add 方法，可以將字符串添加到已有序列的末尾或指定位置，它的本質是一個線程安全的可修改的字符序列。在很多情況下我們的字符串拼接操作不需要線程安全，所以 StringBuilder 登場了。
- StringBuilder 是 JDK1.5 發布的，它和 StringBuffer 本質上沒什麽區别，就是去掉了保證線程安全的那部分，減少了開銷。

線程安全：

- StringBuffer：線程安全
- StringBuilder：線程不安全

速度：

- 一般情況下，速度從快到慢為 StringBuilder > StringBuffer > String，當然這是相對的，不是絕對的。

使用場景：

- 操作少量的數據使用 String。
- 單線程操作大量數據使用 StringBuilder。
- 多線程操作大量數據使用 StringBuffer。


![[IMG-小林  Java 基礎面試題-20250807203922817.png]]


## [#](https://xiaolincoding.com/interview/java.html#java-1-8-%E6%96%B0%E7%89%B9%E6%80%A7)Java 1.8 新特性

### [#](https://xiaolincoding.com/interview/java.html#java%E4%B8%ADstream%E7%9A%84api%E4%BB%8B%E7%BB%8D%E4%B8%80%E4%B8%8B)Java 中 stream 的 API 介紹一下

Java 8 引入了 Stream API，它提供了一種高效且易於使用的數據處理方式，特别适合集合對象的操作，如過濾、映射、排序等。Stream API 不僅可以提高代碼的可讀性和簡潔性，還能利用多核處理器的優勢進行并行處理。讓我們通過兩個具體的例子來感受下 Java Stream API 帶來的便利，對比在 Stream API 引入之前的傳統做法。

> 案例 1：過濾并收集滿足條件的元素

**問題場景**：從一個列表中篩選出所有長度大於 3 的字符串，并收集到一個新的列表中。

**沒有 Stream API 的做法**：

```
List<String> originalList = Arrays.asList("apple", "fig", "banana", "kiwi");
List<String> filteredList = new ArrayList<>();

for (String item : originalList) {
    if (item.length() > 3) {
        filteredList.add(item);
    }
}
```

這段代碼需要顯式地創建一個新的 ArrayList，并通過循環遍曆原列表，手動檢查每個元素是否滿足條件，然後添加到新列表中。

**使用 Stream API 的做法**：

```
List<String> originalList = Arrays.asList("apple", "fig", "banana", "kiwi");
List<String> filteredList = originalList.stream()
                                        .filter(s -> s.length() > 3)
                                        .collect(Collectors.toList());
```

這裏，我們直接在原始列表上調用`.stream()` 方法創建了一個流，使用`.filter()` 中間操作篩選出長度大於 3 的字符串，最後使用`.collect(Collectors.toList())` 終端操作將結果收集到一個新的列表中。代碼更加簡潔明了，邏輯一目了然。

> 案例 2：計算列表中所有數字的總和

**問題場景**：計算一個數字列表中所有元素的總和。

**沒有 Stream API 的做法**：

```
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
int sum = 0;
for (Integer number : numbers) {
    sum += number;
}
```

這個傳統的 for-each 循環遍曆列表中的每一個元素，累加它們的值來計算總和。

**使用 Stream API 的做法**：

```
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
int sum = numbers.stream()
                 .mapToInt(Integer::intValue)
                 .sum();
```

通過 Stream API，我們可以先使用`.mapToInt()` 將 Integer 流轉換為 IntStream（這是為了高效處理基本類型），然後直接調用`.sum()` 方法來計算總和，極大地簡化了代碼。

### [#](https://xiaolincoding.com/interview/java.html#stream%E6%B5%81%E7%9A%84%E5%B9%B6%E8%A1%8Capi%E6%98%AF%E4%BB%80%E4%B9%88)Stream 流的并行 API 是什麽？

是 ParallelStream。

并行流（ParallelStream）就是將源數據分為多個子流對象進行多線程操作，然後將處理的結果再彙總為一個流對象，底層是使用通用的 fork/join 池來實現，即將一個任務拆分成多個 “小任務” 并行計算，再把多個 “小任務” 的結果合并成總的計算結果

Stream 串行流與并行流的主要區别：

![[IMG-小林  Java 基礎面試題-20250807203925456.png]]


對 CPU 密集型的任務來說，并行流使用 ForkJoinPool 線程池，為每個 CPU 分配一個任務，這是非常有效率的，但是如果任務不是 CPU 密集的，而是 I/O 密集的，并且任務數相對線程數比較大，那麽直接用 ParallelStream 并不是很好的選擇。

### [#](https://xiaolincoding.com/interview/java.html#completablefuture%E6%80%8E%E4%B9%88%E7%94%A8%E7%9A%84)completableFuture 怎麽用的？

CompletableFuture 是由 Java 8 引入的，在 Java8 之前我們一般通過 Future 實現異步。

- Future 用於表示異步計算的結果，隻能通過阻塞或者輪詢的方式獲取結果，而且不支持設置回調方法，Java 8 之前若要設置回調一般會使用 guava 的 ListenableFuture，回調的引入又會導致臭名昭著的回調地獄（下面的例子會通過 ListenableFuture 的使用來具體進行展示）。
- CompletableFuture 對 Future 進行了擴展，可以通過設置回調的方式處理計算結果，同時也支持組合操作，支持進一步的編排，同時一定程度解決了回調地獄的問題。

下面將舉例來說明，我們通過 ListenableFuture、CompletableFuture 來實現異步的差異。假設有三個操作 step1、step2、step3 存在依賴關系，其中 step3 的執行依賴 step1 和 step2 的結果。

Future (ListenableFuture) 的實現（回調地獄）如下：

```
ExecutorService executor = Executors.newFixedThreadPool(5);
ListeningExecutorService guavaExecutor = MoreExecutors.listeningDecorator(executor);
ListenableFuture<String> future1 = guavaExecutor.submit(() -> {
    //step 1
    System.out.println("執行step 1");
    return "step1 result";
});
ListenableFuture<String> future2 = guavaExecutor.submit(() -> {
    //step 2
    System.out.println("執行step 2");
    return "step2 result";
});
ListenableFuture<List<String>> future1And2 = Futures.allAsList(future1, future2);
Futures.addCallback(future1And2, new FutureCallback<List<String>>() {
    @Override
    public void onSuccess(List<String> result) {
        System.out.println(result);
        ListenableFuture<String> future3 = guavaExecutor.submit(() -> {
            System.out.println("執行step 3");
            return "step3 result";
        });
        Futures.addCallback(future3, new FutureCallback<String>() {
            @Override
            public void onSuccess(String result) {
                System.out.println(result);
            }        
            @Override
            public void onFailure(Throwable t) {
            }
        }, guavaExecutor);
    }

    @Override
    public void onFailure(Throwable t) {
    }}, guavaExecutor);
```

CompletableFuture 的實現如下：

```
ExecutorService executor = Executors.newFixedThreadPool(5);
CompletableFuture<String> cf1 = CompletableFuture.supplyAsync(() -> {
    System.out.println("執行step 1");
    return "step1 result";
}, executor);
CompletableFuture<String> cf2 = CompletableFuture.supplyAsync(() -> {
    System.out.println("執行step 2");
    return "step2 result";
});
cf1.thenCombine(cf2, (result1, result2) -> {
    System.out.println(result1 + " , " + result2);
    System.out.println("執行step 3");
    return "step3 result";
}).thenAccept(result3 -> System.out.println(result3));
```

顯然，CompletableFuture 的實現更為簡潔，可讀性更好。

![[IMG-小林  Java 基礎面試題-20250807203928098.png]] CompletableFuture 實現了兩個接口（如上圖所示)：Future、CompletionStage。

- Future 表示異步計算的結果，CompletionStage 用於表示異步執行過程中的一個步驟（Stage），這個步驟可能是由另外一個 CompletionStage 觸發的，随着當前步驟的完成，也可能會觸發其他一系列 CompletionStage 的執行。
- 從而我們可以根據實際業務對這些步驟進行多樣化的編排組合，CompletionStage 接口正是定義了這樣的能力，我們可以通過其提供的 thenAppy、thenCompose 等函數式編程方法來組合編排這些步驟。

## [#](https://xiaolincoding.com/interview/java.html#%E5%BA%8F%E5%88%97%E5%8C%96)序列化

### [#](https://xiaolincoding.com/interview/java.html#%E6%80%8E%E4%B9%88%E6%8A%8A%E4%B8%80%E4%B8%AA%E5%AF%B9%E8%B1%A1%E4%BB%8E%E4%B8%80%E4%B8%AAjvm%E8%BD%AC%E7%A7%BB%E5%88%B0%E5%8F%A6%E4%B8%80%E4%B8%AAjvm)怎麽把一個對象從一個 jvm 轉移到另一個 jvm?

- **使用序列化和反序列化**：將對象序列化為字節流，并將其發送到另一個 JVM，然後在另一個 JVM 中反序列化字節流恢複對象。這可以通過 Java 的 ObjectOutputStream 和 ObjectInputStream 來實現。
- **使用消息傳遞機制**：利用消息傳遞機制，比如使用消息隊列（如 RabbitMQ、Kafka）或者通過網絡套接字進行通信，將對象從一個 JVM 發送到另一個。這需要自定義協議來序列化對象并在另一個 JVM 中反序列化。
- **使用遠程方法調用（RPC）**：可以使用遠程方法調用框架，如 gRPC，來實現對象在不同 JVM 之間的傳輸。遠程方法調用可以讓你在分布式系統中調用遠程 JVM 上的對象的方法。
- **使用共享數據庫或緩存**：將對象存儲在共享數據庫（如 MySQL、PostgreSQL）或共享緩存（如 Redis）中，讓不同的 JVM 可以訪問這些共享數據。這種方法适用於需要共享數據但不需要直接傳輸對象的場景。

### [#](https://xiaolincoding.com/interview/java.html#%E5%BA%8F%E5%88%97%E5%8C%96%E5%92%8C%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E8%AE%A9%E4%BD%A0%E8%87%AA%E5%B7%B1%E5%AE%9E%E7%8E%B0%E4%BD%A0%E4%BC%9A%E6%80%8E%E4%B9%88%E5%81%9A)序列化和反序列化讓你自己實現你會怎麽做？

Java 默認的序列化雖然實現方便，但卻存在安全漏洞、不跨語言以及性能差等缺陷。

- 無法跨語言： Java 序列化目前隻适用基於 Java 語言實現的框架，其它語言大部分都沒有使用 Java 的序列化框架，也沒有實現 Java 序列化這套協議。因此，如果是兩個基於不同語言編寫的應用程序相互通信，則無法實現兩個應用服務之間傳輸對象的序列化與反序列化。
- 容易被攻擊：Java 序列化是不安全的，我們知道對象是通過在 ObjectInputStream 上調用 readObject () 方法進行反序列化的，這個方法其實是一個神奇的構造器，它可以將類路徑上幾乎所有實現了 Serializable 接口的對象都實例化。這也就意味着，在反序列化字節流的過程中，該方法可以執行任意類型的代碼，這是非常危險的。
- 序列化後的流態大：序列化後的二進制流大小能體現序列化的性能。序列化後的二進制數組越大，占用的存儲空間就越多，存儲硬件的成本就越高。如果我們是進行網絡傳輸，則占用的帶寬就更多，這時就會影響到系統的吞吐量。

我會考慮用主流序列化框架，比如 FastJson、Protobuf 來替代 Java 序列化。

如果追求性能的話，Protobuf 序列化框架會比較合适，Protobuf 的這種數據存儲格式，不僅壓縮存儲數據的效果好， 在編碼和解碼的性能方面也很高效。Protobuf 的編碼和解碼過程結合.proto 文件格式，加上 Protocol Buffer 獨特的編碼格式，隻需要簡單的數據運算以及位移等操作就可以完成編碼與解碼。可以說 Protobuf 的整體性能非常優秀。

### [#](https://xiaolincoding.com/interview/java.html#%E5%B0%86%E5%AF%B9%E8%B1%A1%E8%BD%AC%E4%B8%BA%E4%BA%8C%E8%BF%9B%E5%88%B6%E5%AD%97%E8%8A%82%E6%B5%81%E5%85%B7%E4%BD%93%E6%80%8E%E4%B9%88%E5%AE%9E%E7%8E%B0)將對象轉為二進制字節流具體怎麽實現？

其實，像序列化和反序列化，無論這些可逆操作是什麽機制，都會有對應的**處理和解析協議**，例如加密和解密，TCP 的粘包和拆包，序列化機制是通過序列化協議來進行處理的，和 class 文件類似，它其實是定義了序列化後的字節流格式，然後對此格式進行操作，生成符合格式的字節流或者將字節流解析成對象。

在 Java 中通過序列化對象流來完成序列化和反序列化：

- ObjectOutputStream：通過 writeObject (）方法做序列化操作。
- ObjectInputStrean：通過 readObject () 方法做反序列化操作。

隻有實現了 Serializable 或 Externalizable 接口的類的對象才能被序列化，否則抛出異常！

實現對象序列化：

- 讓類實現 Serializable 接口：

```
import java.io.Serializable;

public class MyClass implements Serializable {
    // class code
}
```

- 創建輸出流并寫入對象：

```
import java.io.FileOutputStream;
import java.io.ObjectOutputStream;

MyClass obj = new MyClass();
try {
    FileOutputStream fileOut = new FileOutputStream("object.ser");
    ObjectOutputStream out = new ObjectOutputStream(fileOut);
    out.writeObject(obj);
    out.close();
    fileOut.close();
} catch (IOException e) {
    e.printStackTrace();
}
```

實現對象反序列化：

- 創建輸入流并讀取對象：

```
import java.io.FileInputStream;
import java.io.ObjectInputStream;

MyClass newObj = null;
try {
    FileInputStream fileIn = new FileInputStream("object.ser");
    ObjectInputStream in = new ObjectInputStream(fileIn);
    newObj = (MyClass) in.readObject();
    in.close();
    fileIn.close();
} catch (IOException | ClassNotFoundException e) {
    e.printStackTrace();
}
```

通過以上步驟，對象 obj 會被序列化并寫入到文件 "object.ser" 中，然後通過反序列化操作，從文件中讀取字節流并恢複為對象 newObj。這種方式可以方便地將對象轉換為字節流用於持久化存儲、網絡傳輸等操作。需要注意的是，要确保類實現了 Serializable 接口，并且所有成員變量都是 Serializable 的才能被正确序列化。

## [#](https://xiaolincoding.com/interview/java.html#%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)設計模式

### [#](https://xiaolincoding.com/interview/java.html#volatile%E5%92%8Csychronized%E5%A6%82%E4%BD%95%E5%AE%9E%E7%8E%B0%E5%8D%95%E4%BE%8B%E6%A8%A1%E5%BC%8F)volatile 和 sychronized 如何實現單例模式

```
public class SingleTon {

    // volatile 關鍵字修飾變量 防止指令重排序
    private static volatile SingleTon instance = null;
    private SingleTon(){}
     
    public static  SingleTon getInstance(){
        if(instance == null){
            //同步代碼塊 隻有在第一次獲取對象的時候會執行到 ，第二次及以後訪問時 instance變量均非null故不會往下執行了 直接返回啦
            synchronized(SingleTon.class){
                if(instance == null){
                    instance = new SingleTon();
                }
            }
        }
        return instance;
    }
}
```

正确的雙重檢查鎖定模式需要需要使用 volatile。volatile 主要包含兩個功能。

- 保證可見性。使用 volatile 定義的變量，將會保證對所有線程的可見性。
- 禁止指令重排序優化。

由於 volatile 禁止對象創建時指令之間重排序，所以其他線程不會訪問到一個未初始化的對象，從而保證安全性。

### [#](https://xiaolincoding.com/interview/java.html#%E4%BB%A3%E7%90%86%E6%A8%A1%E5%BC%8F%E5%92%8C%E9%80%82%E9%85%8D%E5%99%A8%E6%A8%A1%E5%BC%8F%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB)代理模式和适配器模式有什麽區别？

- **目的不同**：代理模式主要關注控制對對象的訪問，而适配器模式則用於接口轉換，使不兼容的類能夠一起工作。
- **結構不同**：代理模式一般包含抽象主題、真實主題和代理三個角色，适配器模式包含目標接口、适配器和被适配者三個角色。
- **應用場景不同**：代理模式常用於添加額外功能或控制對對象的訪問，适配器模式常用於讓不兼容的接口協同工作。

## [#](https://xiaolincoding.com/interview/java.html#i-o)I/O

### [#](https://xiaolincoding.com/interview/java.html#java%E6%80%8E%E4%B9%88%E5%AE%9E%E7%8E%B0%E7%BD%91%E7%BB%9Cio%E9%AB%98%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B)**Java 怎麽實現網絡 IO 高并發編程？**

可以用 Java NIO ，是一種同步非阻塞的 I/O 模型，也是 I/O 多路複用的基礎。

傳統的 BIO 裏面 socket.read ()，如果 TCP RecvBuffer 裏沒有數據，函數會一直阻塞，直到收到數據，返回讀到的數據， 如果使用 BIO 要想要并發處理多個客戶端的 i/o，那麽會使用多線程模式，一個線程專門處理一個客戶端 io，這種模式随着客戶端越來越多，所需要創建的線程也越來越多，會急劇消耗系統的性能。
![[IMG-小林  Java 基礎面試題-20250807204002515.png]]

NIO 是基於 I/O 多路複用實現的，它可以隻用一個線程處理多個客戶端 I/O，如果你需要同時管理成千上萬的連接，但是每個連接隻發送少量數據，例如一個聊天服務器，用 NIO 實現會更好一些。
![[IMG-小林  Java 基礎面試題-20250807203937850.png]]

### [#](https://xiaolincoding.com/interview/java.html#bio%E3%80%81nio%E3%80%81aio%E5%8C%BA%E5%88%AB%E6%98%AF%E4%BB%80%E4%B9%88)BIO、NIO、AIO 區别是什麽？

- BIO（blocking IO）：就是傳統的 java.io 包，它是基於流模型實現的，交互的方式是同步、阻塞方式，也就是說在讀入輸入流或者輸出流時，在讀寫動作完成之前，線程會一直阻塞在那裏，它們之間的調用是可靠的線性順序。優點是代碼比較簡單、直觀；缺點是 IO 的效率和擴展性很低，容易成為應用性能瓶頸。
- NIO（non-blocking IO） ：Java 1.4 引入的 java.nio 包，提供了 Channel、Selector、Buffer 等新的抽象，可以構建多路複用的、同步非阻塞 IO 程序，同時提供了更接近操作系統底層高性能的數據操作方式。
- AIO（Asynchronous IO） ：是 Java 1.7 之後引入的包，是 NIO 的升級版本，提供了異步非堵塞的 IO 操作方式，所以人們叫它 AIO（Asynchronous IO），異步 IO 是基於事件和回調機制實現的，也就是應用操作之後會直接返回，不會堵塞在那裏，當後台處理完成，操作系統會通知相應的線程進行後續的操作。

### [#](https://xiaolincoding.com/interview/java.html#nio%E6%98%AF%E6%80%8E%E4%B9%88%E5%AE%9E%E7%8E%B0%E7%9A%84)NIO 是怎麽實現的？

NIO 是一種同步非阻塞的 IO 模型，所以也可以叫 NON-BLOCKINGIO。同步是指線程不斷輪詢 IO 事件是否就緒，非阻塞是指線程在等待 IO 的時候，可以同時做其他任務。

同步的核心就 Selector（I/O 多路複用），Selector 代替了線程本身輪詢 IO 事件，避免了阻塞同時減少了不必要的線程消耗；非阻塞的核心就是通道和緩沖區，當 IO 事件就緒時，可以通過寫到緩沖區，保證 IO 的成功，而無需線程阻塞式地等待。

NIO 由一個專門的線程處理所有 IO 事件，并負責分發。事件驅動機制，事件到來的時候觸發操作，不需要阻塞的監視事件。線程之間通過 wait,notify 通信，減少線程切換。

NIO 主要有三大核心部分：Channel (通道)，Buffer (緩沖區), Selector。傳統 IO 基於字節流和字符流進行操作，而 NIO 基於 Channel 和 Buffer (緩沖區) 進行操作，數據總是從通道讀取到緩沖區中，或者從緩沖區寫入到通道中。

Selector (選擇區) 用於監聽多個通道的事件（比如：連接打開，數據到達）。因此，單個線程可以監聽多個數據通道。
![[IMG-小林  Java 基礎面試題-20250807203943849.png]]
![[100_attachements/40bb3e279e41c896ab143767a29eb519_MD5.webp]]

### [#](https://xiaolincoding.com/interview/java.html#%E4%BD%A0%E7%9F%A5%E9%81%93%E6%9C%89%E5%93%AA%E4%B8%AA%E6%A1%86%E6%9E%B6%E7%94%A8%E5%88%B0nio%E4%BA%86%E5%90%97)你知道有哪個框架用到 NIO 了嗎？

**Netty。**

Netty 的 I/O 模型是基於非阻塞 I/O 實現的，底層依賴的是 NIO 框架的多路複用器 Selector。采用 epoll 模式後，隻需要一個線程負責 Selector 的輪詢。當有數據處於就緒狀态後，需要一個事件分發器（Event Dispather），它負責將讀寫事件分發給對應的讀寫事件處理器（Event Handler）。事件分發器有兩種設計模式：Reactor 和 Proactor，Reactor 采用同步 I/O， Proactor 采用異步 I/O。

![[IMG-小林  Java 基礎面試題-20250807203946364.png]]
Reactor 實現相對簡單，适合處理耗時短的場景，對於耗時長的 I/O 操作容易造成阻塞。Proactor 性能更高，但是實現邏輯非常複雜，适合圖片或視頻流分析服務器，目前主流的事件驅動模型還是依賴 select 或 epoll 來實現。

## [#](https://xiaolincoding.com/interview/java.html#%E5%85%B6%E4%BB%96)其他

### [#](https://xiaolincoding.com/interview/java.html#%E6%9C%89%E4%B8%80%E4%B8%AA%E5%AD%A6%E7%94%9F%E7%B1%BB-%E6%83%B3%E6%8C%89%E7%85%A7%E5%88%86%E6%95%B0%E6%8E%92%E5%BA%8F-%E5%86%8D%E6%8C%89%E5%AD%A6%E5%8F%B7%E6%8E%92%E5%BA%8F-%E5%BA%94%E8%AF%A5%E6%80%8E%E4%B9%88%E5%81%9A)有一個學生類，想按照分數排序，再按學號排序，應該怎麽做？

可以使用 Comparable 接口來實現按照分數排序，再按照學號排序。首先在學生類中實現 Comparable 接口，并重寫 compareTo 方法，然後在 compareTo 方法中實現按照分數排序和按照學號排序的邏輯。

```
public class Student implements Comparable<Student> {
    private int id;
    private int score;

    // 構造方法和其他屬性、方法省略

    @Override
    public int compareTo(Student other) {
        if (this.score != other.score) {
            return Integer.compare(other.score, this.score); // 按照分數降序排序
        } else {
            return Integer.compare(this.id, other.id); // 如果分數相同，則按照學號升序排序
        }
    }
}
```

然後在需要對學生列表進行排序的地方，使用 Collections.sort () 方法對學生列表進行排序即可：

```
List<Student> students = new ArrayList<>();
// 添加學生對象到列表中
Collections.sort(students);
```

### [#](https://xiaolincoding.com/interview/java.html#native%E6%96%B9%E6%B3%95%E8%A7%A3%E9%87%8A%E4%B8%80%E4%B8%8B)Native 方法解釋一下

在 Java 中，native 方法是一種特殊類型的方法，它允許 Java 代碼調用外部的本地代碼，即用 C、C++ 或其他語言編寫的代碼。native 關鍵字是 Java 語言中的一種聲明，用於標記一個方法的實現將在外部定義。

在 Java 類中，native 方法看起來與其他方法相似，隻是其方法體由 native 關鍵字代替，沒有實際的實現代碼。例如：

```
public class NativeExample {
    public native void nativeMethod();
}
```

要實現 native 方法，你需要完成以下步驟：

1. **生成 JNI 頭文件**：使用 javah 工具從你的 Java 類生成 C/C++ 的頭文件，這個頭文件包含了所有 native 方法的原型。
2. **編寫本地代碼**：使用 C/C++ 編寫本地方法的實現，并确保方法簽名與生成的頭文件中的原型匹配。
3. **編譯本地代碼**：將 C/C++ 代碼編譯成動态鏈接庫（DLL，在 Windows 上），共享庫（SO，在 Linux 上）
4. **加載本地庫**：在 Java 程序中，使用 System.loadLibrary () 方法來加載你編譯好的本地庫，這樣 JVM 就能找到并調用 native 方法的實現了。