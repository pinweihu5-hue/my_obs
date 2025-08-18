[[CS186 Introduction to Database System]]





![[IMG-186-11-Iterators, Relational Operators and Joins-20241102165237270.png|727]]


![[IMG-186-11-Iterators, Relational Operators and Joins-20241102165237441.png|767]]



![[IMG-186-11-Iterators, Relational Operators and Joins-20241102165237642.png|493]]


`R + R*S`
![[IMG-186-11-Iterators, Relational Operators and Joins-20241102165237770.png|692]]


![[IMG-186-11-Iterators, Relational Operators and Joins-20241102165237890.png|545]]



`R + R * (B+tree的 IO, 個位數)`
跟上面比較
40,000 -> 變成個位數
![[IMG-186-11-Iterators, Relational Operators and Joins-20241102165238009.png|729]]


![[IMG-186-11-Iterators, Relational Operators and Joins-20241102165238131.png|555]]

動畫看ptt


`4*R + 4*S + R + S`
![[IMG-186-11-Iterators, Relational Operators and Joins-20241102165238240.png|692]]



`3*R + 3*S`
![[IMG-186-11-Iterators, Relational Operators and Joins-20241102165238360.png|676]]



![[IMG-186-11-Iterators, Relational Operators and Joins-20241102165238489.png|531]]

`3(R+S)`
![[IMG-186-11-Iterators, Relational Operators and Joins-20241102165238652.png|632]]



比較兩種資料庫中常用的連接 (Join) 演算法：**雜湊連接 (Hash Join)** 和 **排序合併連接 (Sort-Merge Join)**

(Sort-Merge Join) 排序合併連接的優點
- **適用於已排序或需要排序輸出：** 如果輸入資料已經排序，或者需要輸出排序後的結果，那麼排序合併連接是一個很好的選擇。
- **不受資料偏斜和雜湊函數影響：** 排序合併連接不受資料偏斜 (data skew) 或雜湊函數好壞的影響，穩定性較高。
    

 (Hash Join)雜湊連接的優點
- **連接效率取決於較小關係的大小：** 雜湊連接所需的遍歷次數取決於較小關係的大小。
    - 例如，如果較小關係的大小比記憶體緩衝區 (B) 小，那麼簡單 / 混合雜湊 (naive/hybrid hashing) 就能表現得很好。(因為可以直接放在 B 裡面)
- **適用於已雜湊或需要雜湊輸出：** 如果輸入資料已經雜湊，或者需要輸出雜湊後的結果，那麼雜湊連接是一個很好的選擇。
    

**總結：**
- 排序合併連接適用於輸入資料已排序或需要排序輸出，且不受資料偏斜和雜湊函數影響的情況。
- 雜湊連接適用於較小關係的大小比記憶體緩衝區小，或輸入資料已雜湊或需要雜湊輸出的情況。
    



![[IMG-186-11-Iterators, Relational Operators and Joins-20241102165238793.png|590]]



Cost model
- nested loop join `R + R*S`
- index nested loop  `R + R*(B+tree的 IO, 個位數, 3~5)`
- sort-marge `3*R + 3*S`
- hash join `3(R+S)`