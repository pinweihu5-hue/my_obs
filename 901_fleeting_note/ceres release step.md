[[ceres]]

---

ref:  ceres relase note can see [ceres service doc](https://docs.google.com/document/d/1IRpQYD-9SWjmQWdUAvgx3gYD4bqisT0qX9snIJmbmvQ/edit?hl=zh-TW&forcehl=1)



### Steps

0
去 announcement engineer chat 預告



1
確認本週要release 的 branch 都已經合進去 master

2
從 master 拉出 release branch 

branch name 為  `release/x.xx.x`

修改package.json 異動版號 
npm install 
-> 確認package.json & package-lock.json 都已改完版號


example:
![[IMG-ceres release step-20241003104933828.png]]


4
push  release branch  to remote
新開 MR (release/x.x.x  ->  master )


![[IMG-ceres release step-20241003104948773.png]]


5
確認 CICD 任務都完成 ( test & build image  )

![[IMG-ceres release step-20241003105001540.webp|497]]



6
在 release branch 打 tag (push tag to remote)
這邊不是選 **release**, 是選 **Tag**
這邊的動作等同你在 local 打 tag 推上 remote
![[IMG-ceres release step-20241003105001643.webp]]




7
2點時，去打 tag 的那個 pipeline
手動觸發 deploy 任務


![[IMG-ceres release step-20241003105018911.webp]]


8
go to http://104.199.207.192/version 確認 ceres 已更新新版號
沒問題把 release branch 和入 master




---

