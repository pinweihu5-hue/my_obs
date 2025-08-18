---  
alias:  
creation_date: 2024-05-19 21:42  
tags: 
---  
[[tool optimize]],  [[vscode_idx]]





# git operation

## 切branch 可以按左下, say 切去 qat

![[IMG-vscode_inteillJ切換-20241003104934070.png|200]]

##  merge 
可以 f1 and then choose branch to merge from, say 把自己的 feat 和進去 qat
前面有 cloud 的就是遠端
![[IMG-vscode_inteillJ切換-20241003104948937.png|600]]


##  check b 狀態 sync 的狀態
![[IMG-vscode_inteillJ切換-20241003105011276.png]]


## merge my feat into qat 要清理 working tree
- tsc --watch ->  gen new js
	- 會讓讓 qat gen changed file -> 如果 qat 有 missing
- 這時候你如果要 merge other into qat -> 會沒反應, 因為 qat 的 working tree 是髒的，不讓你 merge

## 砍 local branch
這邊砍即可
![[IMG-vscode_inteillJ切換-20241003105037237.png|300]]







# vscode git 操作
- remove qat
	- `git checkout -b origin/qat
	- useful command
	- Git: View Staged Changes
	- Git: View Changes
	- Git: View Untracked Changes. -> 
	- git.stashIncludeUntracked -> 還沒 staged

# vscode 沒有，但是希望弄出來的
- 自動偵測 node module 不一致，提醒
- changelist, unstaging/change list的分類
	- 
		- create qat after remove it
- 自動 shelve or unshelve


- intelliJ 的慢，已經到了可以打斷心流的地步
- 用 vim 的好處就是不會打斷心流
	- 但有前提，需要你有辦法肌肉記憶哪些熱鍵然後不會被打斷


current theme
![[IMG-vscode_inteillJ切換-20241003105102386.png|200]]
![[IMG-vscode_inteillJ切換-20241003105123504.png|200]]


# toggle explorer view
目前用 https://stackoverflow.com/a/69099240/7621417
