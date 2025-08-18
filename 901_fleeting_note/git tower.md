---  
alias:  
creation_date: 2024-05-24 14:45  
tags: 
related: [[git_idx|git]], [[vscode_inteillJ切換]], [[tool optimize]]
---  


tower 加入 vscode
https://github.com/SushiFu/tower-diff-merge-vscode

keyboard
https://www.git-tower.com/help/guides/faq-and-tips/keyboard-shortcuts/mac

tip
[Tips & Tricks | Tower Help](https://www.git-tower.com/help/guides/faq-and-tips/tips-and-tricks/mac)



# tower 優點
- drag to publish (send local to remote first time)
- drag to create branch
- darg to cherry pick (to local or to any brnach)
- cmd+z undo a lot of things
	- delete b or tag
	- merge, rebase, interative rebase
	- commit
	- checkout
	- staging, unstaging
	- ...
- **cmd + shifr + z 來 undo undo**


# tower 操作記錄
## 改過去的 commit
![[IMG-git tower-20241003104933929.png| 500]]
你會需要 解跟上面 a5, a6 的衝突
另外你要 sync 上去的話，也要解跟 remote 的衝突



## use alt/cmd 然後 hover commit -> ammend
![[IMG-git tower-20241003104948851.png|300]]


如果你要 ammend 的前的東西已經 推上remote
這樣你本地改變 local commit 的行為會 divergent  to remote
那你推上去會需要 解衝突


## 本地整理一: squash 很多 commit 然後強堆
- 這邊要用 force push with lease 
	- 可以避免意外覆蓋他人的工作
	- 會檢查本地分支和遠程分支的提交歷史是否已經發生變化
	- 如果有變化，表示有其他人已經推送過
	- 你如果強推，會蓋掉其他人的工作
	- 如果沒有變化，那你就可以強堆
	- 你還是會蓋掉"你自己"的工作

![[IMG-git tower-20241003105005394.png|400]]


## reflog to get back the delete commit (hard reset)

- 不過 hard rest 也可以 redo
	- a lot of things you can ^z redo in tower
	- 不過 redo 是你發生後需要馬上才可以做的事情
	- 如果你弄錯了，又做了很多操作 -> 你就需要用 reflog 了!!

![[IMG-git tower-20241003105027006.png|500]]


然後你可以確認這個 new B 是否一有拿回你要的
然後你就可以切過去，砍掉舊的，確認 tracking 有到對的 remote, done

PS: 另外reflog 的紀錄都可以 apply 到 你的 branch



## Sync to match to remote 


然後，如果你 squash 你的 commits 後，你直接用 sync, 你會蓋掉你剛剛的工作 XDD
因為 sync 的功能是讓你跟遠端對齊，不是你推到遠端
![[IMG-git tower-20241003105051787.png|400]]


## squash 很多 commits or soft-reset 到一開始再推一次
- 結果一樣
- 操作困難度差不多
	- squash 比較簡單一點，因為一個操作，不需要reset 再 commit




## stash

建議不要 stash untracked file
可能會導致這些因為被 ignore 所以是 untracked 的檔案，又變成不是ignore 狀態
https://www.git-tower.com/help/guides/working-copy/stash/mac


加上 alt 可以快速 stash and snapshot -> 幫你上 stash title 的意思


可以拉部分的 stash

tower 也可以自動 re-apply的 stash -> 叫做 snapshot
- 跟 stash 不同，你 snapshot的東西不會從 wrk copy 移走
- 當你你從 b1 切到 b2 -> 他會問你要不要把 b1 stach 的東西，自動 re-apply 到 b2
- 當你切回來，一樣回問你
- 幫你只是想要過去看東西，想要切過去一下，等下會回來，不是要長期存的，用這個很方便


## 其他操作

- [No. 1 - Discarding All Local Changes in a File](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/discard-local-changes/mac)
- [No. 2 - Restoring a Deleted File](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/restore-deleted-file/mac)
- [No. 3 - Discarding Chunks / Lines in a File](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/discard-chunks-lines/mac)
- [No. 4 - Discarding All Local Changes](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/discard-all-changes/mac)
- [No. 5 - Fixing the Last Commit](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/amend-fix-last-commit/mac)
- [No. 6 - Reverting a Commit in the Middle](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/revert-commit/mac)
- [No. 7 - Resetting to an Old Revision](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/rollback-reset/mac)
- [No. 8 - Resetting a File to an Old Revision](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/reset-single-file/mac)
- [No. 9 - Recovering Deleted Commits](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/undo-reset-with-reflog/mac)
- [No. 10 - Recovering a Deleted Branch](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/undo-branch-delete-reflog/mac)
- [No. 11 - Moving a Commit to a New Branch](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/move-commit-to-new-branch/mac)
- [No. 12 - Moving a Commit to a Different Branch](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/move-commit-to-existing-branch/mac)
- [No. 13 - Editing Old Commit Messages](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/rebase-i-edit-commit-message/mac)
- [No. 14 - Deleting Old Commits](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/rebase-i-delete-commit/mac)
- [No. 15 - Squashing Multiple Commits into One](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/rebase-i-squash-commits/mac)
- [No. 16 - Adding a Change to an Old Commit](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/rebase-i-fixup/mac)
- [No. 17 - Splitting / Editing an Old Commit](https://www.git-tower.com/help/guides/faq-and-tips/undoing-things/rebase-i-split-commits/mac)



## 圖片
這邊要註冊 https://gravatar.com/




## rebase vs merge


原本線圖 for feat_5
![[IMG-git tower-20241003105120265.png|500]]


rebase線圖, feat_5 onto f6
可以看到 f5 的線直接"接在 f6 後面"
![[IMG-git tower-20241003105132757.png|500]]


merge線圖
	可以看到 f6 就直接 merge 進去 f5
![[IMG-git tower-20241003105141671.png|500]]


如果你要 squash when merge
有可能會出現衝突，因為 git 每一個點被你壓縮，b and b 間無法判定一些狀態了
![[IMG-git tower-20241003105149157.png]]





# 差異 vs fork

- tower 多了很多可以 drag 
- 點 branch 
	- tower 只會顯示那個 branch 的線圖
		- 多選來看多個
	- fork 顯示的全部的線圖，然後會 focus 到那到
		- 搭配 filter+ eye 來看
