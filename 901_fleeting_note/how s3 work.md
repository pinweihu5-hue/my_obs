
更具體一點
S3 支援 100 million req per sec and unlimited amount of storage
how they achievei it?

下面是 s3 的 high level design
![[IMG-how s3 work-20250810200236900.png]]

# auto scale 的 web server

就可以支援超級大量的 req per sec




## 放在機械硬碟，所以可以降低成本
> 並使用 [ShardStore](https://assets.amazon.science/77/5e/4a7c238f4ce890efdc325df83263/using-lightweight-formal-methods-to-validate-a-key-value-storage-node-in-amazon-s3-2.pdf) 組織硬碟上的資料 - 它提供了更好的性能。
> 想像 **ShardStore** 視為日誌結構合併 ( **[LSM](https://en.wikipedia.org/wiki/Log-structured_merge-tree)** ) 樹資料結構的變體。



## LSM 結構，讓他們寫入資料夠快。append only, O(1)
also see [[storage and retrieval DB engine]]



_replicate_ _data_ across many disks and do _parallel_ _reads_ - it gives higher throughput
![[IMG-how s3 work-20250810200237847.png]]


## Erasure Coding -> 高可用和儲存效率
see [[Erasure Coding（擦除編碼）]]







ref:
[S3 架構 - Neo Kim 著 - 系統設計時事通訊 --- S3 Architecture - by Neo Kim - System Design Newsletter](https://newsletter.systemdesign.one/p/s3-architecture?utm_source=post-email-title&publication_id=1511845&post_id=150109909&utm_campaign=email-post-title&isFreemail=true&r=85bw7&triedRedirect=true&utm_medium=email)