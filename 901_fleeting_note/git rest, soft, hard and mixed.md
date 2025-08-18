
- [[git_idx|git]]


- hard
    - reset 三種： head, idx and working dir  到你指定的 commit
    - **很危險, 這個 commit 後的東西都會消失**
    - 除非你真的非常非常確定
    - 建議先 copy 一個 backup_b 出來再弄
    - use case:
	    - 你確定確定確定你東西要丟掉用
- mixed
    - reset 兩種: idx and head
    - 因為沒有 reset 到 working dir → 這個 commit 後的東西，會在 working dir
    - 會reset idx了，因此也不會在staged了
    - use case:
	    - 建議你真的要砍也用 mixed, 然後你再手動去 discard掉
- soft
    - reset only head
    - idx, 和 working dir 都沒動
    - idx 可以想成，你存到 staged會建立 idx → 因此，這個 commit 後改的東西會在 staged
    - use case:
        - 想要把很多commits壓成一個 commit, soft reset 到最開始你拉出來的那個b，類似 dev
        - 這樣你所有的改動都會變到那個 commit and staged好
        - 然後再 commit 即可變成一個 commit
- soft vs mixed
    - 其實你用 mixed 也可以，但是你要多一個步驟，就是需要 重新 staged 再 commit
    - 但是因為 stagged 也可以 unstagged, 因此 mixed and soft 都可以用就是了, 兩個差在 有沒有 staged(index)是否有被reset