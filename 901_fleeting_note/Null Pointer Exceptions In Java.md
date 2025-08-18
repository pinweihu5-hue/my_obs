[Null Pointer Exceptions In Java - What EXACTLY They Are and How to Fix Them - YouTube](https://www.youtube.com/watch?v=lm72_HCd17s&list=WL&index=19&t=2s)


發生情境
使用 null  會發生
![[IMG-Null Pointer Exceptions In Java-20250805201249649.png|825]]


null 可以塞到 Boolean -> check 也會發生
因此這邊建議用 boolean
![[IMG-Null Pointer Exceptions In Java-20250805201249778.png]]
![[IMG-Null Pointer Exceptions In Java-20250805201249924.png]]


for loop null
![[IMG-Null Pointer Exceptions In Java-20250805201250078.png]]

因此建議塞 空 arr
![[IMG-Null Pointer Exceptions In Java-20250805201250242.png]]



how to handle?

null check like below
![[IMG-Null Pointer Exceptions In Java-20250805201250398.png]]

有時候的確很麻煩
![[IMG-Null Pointer Exceptions In Java-20250805201250596.png]]


函數盡量不要return null
![[IMG-Null Pointer Exceptions In Java-20250805201250744.png]]


return empty 那個 object
![[IMG-Null Pointer Exceptions In Java-20250805201250883.png]]


這樣會噴，如果 myOtherCat is null
![[IMG-Null Pointer Exceptions In Java-20250805201251041.png]]

可以反過來寫避免

![[IMG-Null Pointer Exceptions In Java-20250805201251210.png]]