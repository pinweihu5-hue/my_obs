In public classes, use accessor methods, not public fields


有時候你會看到下面這種"退化類"
類的功能只是包住兩個實例欄位
![[IMG-Item16 在公共类中使用访问方法而不是公共属性 v0-20250805201145787.png]]


首先，沒有封裝好處 (Item 15) (You can’t change the representation without changing the API, you can’t enforce invariants, and you can’t take auxiliary action when a field is accessed.)


強硬派 OOP 會想要寫下面這樣，加上 getter and setter
![[IMG-Item16 在公共类中使用访问方法而不是公共属性 v0-20250805201146047.png]]

如果這個類是公開的，這是對的。
getter 讓你保有改變內部結構的彈性。


但如果類是包私有，或是私有嵌套類，那暴露這些欄位可以考慮（assuming they do an adequate job of describing the abstraction provided by the class）

好處，code 比較少，好讀。
的確是讓 使用者跟內部實作耦合了，但是如果是包的 level 或是 class 的 level，那就可能還可以接受。

＝

java.awt 包的 Point and Dimension classes 算是違背的此原則，而這也造成後續這兩個類的有嚴重的效能問題，到今天都是。
(為了向前相容，因此無法輕易調整 API, 因為 API 跟內部實作耦合了)


==
不要讓公開類有公開欄位通常都是一個好點子，但是如果真的一定要，那如果欄位可以是 immutable 傷害會小一點。

You still can’t change the representation of such a class without changing its API, and you can’t take auxiliary actions when a field is read, but you can enforce invariants. 



下面是一個例子，這個類確認每一個實例的 time 是 valid的


![[IMG-Item16 在公共类中使用访问方法而不是公共属性 v0-20250805201146493.png]]


In summary
- public classes should **never** expose mutable fields. 
- It is less harmful, though still questionable, for public classes to expose immutable fields. 
- It is, however, sometimes desirable for package-private or private nested classes to expose fields, whether mutable or immutable.