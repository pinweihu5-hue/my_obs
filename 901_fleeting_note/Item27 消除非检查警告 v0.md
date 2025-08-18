Eliminate unchecked warnings 


盡量修正所有的這種  unchecked warning


在 intelliJ IDE 會是這樣高亮顯示
![[IMG-Item27 消除非检查警告 v0-20250805201146323.png|842]]


如果你可以全部休掉，表示 IDE 幫你移除 `ClassCastException` 噴掉 at runtime




像是
unchecked cast warnings
unchecked method invocation warning
unchecked parameterized vararg type warnings, and unchecked conversion warnings. 等等


很好解的像是
![[IMG-Item27 消除非检查警告 v0-20250805201146772.png]]


加上 `<>` 就解了 -> `Set<Lark> exaltation = new HashSet<>();`




If you can’t eliminate a warning, but you can prove that the code that provoked the warning is typesafe, then (and only then) suppress the warning with an `@SuppressWarnings("unchecked")` annotation. 

不要忽略!
如果忽略，最後一堆 unchecked warning 也沒有 comment 的話， 就變成false alert 
這個 warning 就沒意義了，那你就很容易遇到 `ClassCastException` at run time




SuppressWarnings annotation 只能放在 delcaration,  要確認 scope 越小越好
就算需要拉一個變數出來處理，也值得的。

ex
```java fold

public class ToArrayExample {


    // 將 list 都被複製到 a
    public static <T> T[] toArray(T[] a, List<T> list) {
        int size = list.size();
        // 如果目標陣列太小，則會建立一個新的、大小合適的陣列
        if (a.length < size) {

            // This cast is correct because the array we're creating 
            // is of the same type as the one passed in, which is T[].
            @SuppressWarnings("unchecked") 
            T[] result = (T[]) Arrays.copyOf(list.toArray(), size, a.getClass());
            
//            return (T[]) Arrays.copyOf(list.toArray(), size, a.getClass());
        }

        // 如果目標陣列 a 的長度足夠大
        // 則使用 System.arraycopy() 方法將 elements 陣列中的所有元素複製到 a 陣列中。
        // System.arraycopy() 是一個高效的陣列複製方法。
        System.arraycopy(list.toArray(), 0, a, 0, size);

        // 如果目標陣列太大，表示陣列有多餘的空間, 則會將多餘的部分設為 null
        if (a.length > size) {
            a[size] = null;
        }
        return a;
    }

    public static void main(String[] args) {
        // Example with Strings
        List<String> stringList = new ArrayList<>();
        stringList.add("apple");
        stringList.add("banana");
        stringList.add("orange");

        String[] stringArray1 = new String[stringList.size()]; // Array of the correct size
        String[] filledStringArray1 = toArray(stringArray1, stringList);
        System.out.println("Filled String Array 1: " + Arrays.toString(filledStringArray1));


        String[] stringArray2 = new String[2]; // Array too small
        String[] filledStringArray2 = toArray(stringArray2, stringList);
        System.out.println("Filled String Array 2: " + Arrays.toString(filledStringArray2));


        String[] stringArray3 = new String[5]; // Array too large
        String[] filledStringArray3 = toArray(stringArray3, stringList);
        System.out.println("Filled String Array 3: " + Arrays.toString(filledStringArray3));



        // Example with Integers
        List<Integer> integerList = new ArrayList<>();
        integerList.add(1);
        integerList.add(2);
        integerList.add(3);

        Integer[] integerArray = new Integer[integerList.size()];
        Integer[] filledIntegerArray = toArray(integerArray, integerList);
        System.out.println("Filled Integer Array: " + Arrays.toString(filledIntegerArray));

    }
}

```


### summary
- unchecked warnings are important. Don’t ignore them. 
- Every unchecked warning represents the potential for a `ClassCastException` at runtime.  so, do your best to eliminate these warnings. 
- If you can’t eliminate an unchecked warning and you can prove that the code that provoked it is typesafe, suppress the warning with a `@SuppressWarnings("unchecked")` annotation in the narrowest possible scope. Record the rationale for your decision to suppress the warning in a comment.