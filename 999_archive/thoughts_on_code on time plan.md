
# study 要夠全面 (有必要仔細就仔細)
- 主要是看 source code, 抓出預計需要修改的點
   - 開發下去才發現少必要欄位，進而影響到需要動到 ceres
   - 或是發現以前的架構跟目前里程費的設計不兼容 → 因此需要大修，或是重寫
      - 以上都大幅提高預計的 effort


# study 也包括砍 code 的 ticket
  - 評估砍掉 old code 的 額外 effort and risk 可能常常會大於等於 maintain old code + patch 價值
    - 砍掉 old code 需要跟 PM 確認，也要花時間
  - 如果對 code 很不熟，看看 code 很不夠細
    - 需要 POC 才可以抓到是否缺關鍵欄位 → 可能影響到滿大的 scope 差異
- 需要砍的舊 code 需要釐清是否可以砍，也要規劃出來，避免影響後續開發的複雜度
   - 規劃的時候，考慮到既有邏輯本來就有一定的複雜度，沒有規劃到夠細，大方向上認為，應該可以順著本來的邏輯把里程費添加下去
   - 開發的時候，發現全部順著既有邏輯長下去會額外多出很多沒必要的 effort
      - 沒必要的意思是說，既有邏輯有 dead code/已經 sun set 的 feat
      - 因此變成，部分邏輯可能要先拔然後再開發新功能，effort 才會比較小，技術 debt 也不會繼續長下去。
      - 那為何一開始沒有想到？ → 因為一開始規劃的觀念比較像是：不想要額外去拔舊有的 code 再開發，覺得這樣好像自己把 scope 擴大了。→ 但開發下去才發現一直 patch 下去的 code 會讓未來更難維護，甚至說，寫測試時，就已經發現需要包含 sunset feat 下的沒必要的複雜度了。


# 寫 code 要讓未來好砍，這個比 DRY 重要
- 寫新 api 版本的 test 時，舊版已經測試的測例子，也要在新版複製一套
  - 這樣砍 old ver test, 才好砍
  - 如果你新版只測試新的情況，那到時候要砍舊版 api and its test, 因為你舊版 test mock up 作法可能不同，你要花時間把舊版的 test migrate 到新版的 test 上，這樣就比較麻煩


# 不要面相畫面開發
- 進行複雜的轉換，就算了給前端 -> 面相前端開發(這是不好的 practice)
  - 不好，因為把後端引入沒必要的複雜轉換邏輯
  - 應該回給他們可以自己組的
  - 不是後端不想要做事情，是因為後端主要的邏輯就是面相資料面，面相 entity 的結構


- 一個資料從 DB 撈出來，進入不同的函數處理，每一個函數處理的 input and output 因為職責不同，都需要不太一樣的資料結構
  - 不同的前端可能有自己想要的畫面資料結構，他們要自己組
  - 然後就另外把每個資料定義出自己的 arg and return type, 然後進去都用 casting! 避開了處理資料從 db 出來的 shape 相容問題
  - 債留給後面的處理!


# R.isEmpty(orderIds2.length) -> always false

R.isEmpty(orderIds2.length) -> 永遠都是 false, 因為有數字就不是 empty

如果是 0 -> false -> 繼續跑 -> 但後面 catch 會給 order as []
如果非0 -> 一樣是 false -> 繼續跑 -> 後面 catch 會給正常的 order

應該不會暴，但是 後面的邏輯會撈不到 order 的資料


refactor -> 然後這邊沒有測試這個用例 -> 然後因為我 refactor 的地方不是我改的部份，因此我沒有去調整那邊的 code 去調整到測試到那麼細