不同的客戶要有各自專屬的接口/介面，讓彼此不會互相干擾 → 實現 SRP的 方法


- 站在客戶端的角度，指導如何設計接口 → 接口設計的指導方針
- 接口內的屬性要 fit，不用設計一個通用的讓實做的類需要用到他不需要的, 類再去實作多個接口



# 20個組件都依賴 job class...

就算是 a small typo chnage in Job, 20的組件都要重新 compile
![[IMG-SOLID_ISP 介面隔離 interface segregation principle-20250807202920296.png]]

然後 也因為 太多人依賴 JOB
因此改了 JOB 的一個東西，要去盤點/debug 有哪些可能的影響
![[IMG-SOLID_ISP 介面隔離 interface segregation principle-20250807202921391.png|820]]


解決方法，拆!
![[IMG-SOLID_ISP 介面隔離 interface segregation principle-20250807202922607.png]]

# ISP 真正的意思
![[IMG-SOLID_ISP 介面隔離 interface segregation principle-20250807202924054.png|825]]

# 例子
![[IMG-SOLID_ISP 介面隔離 interface segregation principle-20250807202925434.png|776]]

![[IMG-SOLID_ISP 介面隔離 interface segregation principle-20250807202927360.png]]



# 介面提供的方法，應該對特定的 client 內聚
![[IMG-SOLID_ISP 介面隔離 interface segregation principle-20250807202929187.png]]


# 擴展解釋
![[IMG-SOLID_ISP 介面隔離 interface segregation principle-20250807202930304.png]]


![[IMG-SOLID_ISP 介面隔離 interface segregation principle-20250807202931039.png]]