

Git Flow 

### 核心 branch
- main: 代表着 "**已经发布到生产环境**" 的稳定版本
- dev: 整合所有開發功能
- feat /2/3/4: 開發新功能的分支，從 dev 拉出來，完成後合併回 dev。
- hotfix: 修復生產環境緊急問題的分支，從 master 拉出來，完成後合併回 main 和 dev。
- release: 準備發布版本的分支，從 dev 拉出來，完成後合併回 main 和 dev。

### flow
- 從 dev 拉出來 feat 1
- feat 1 開發完 merge 到 dev
- 要 release 的話, dev -> 拉一個 release branch 出來
	- ex: from 1.34.0 → 1.35.0
	- name: `release/1.35.0`
	- update package.json and its lock
	- commit and push to remote
    - 拉兩個mr 出來，一個 to dev, 一個 to master, 再合併回 dev and main
- 如果master有 bug
	- master 拉出來修 name:` hotfix/xxxxxx`
	- 修完，合回去 main and dev


![[IMG-what is git flow-20250807203328199.png]]




為何 release 是從 從 develop 建立，不是從 master 建立？

1. **隔离主分支 (Master Isolation):**
   - `master` 分支在 Git Flow 中被视为是极其神圣的，它永远代表着已经发布到生产环境的稳定版本。
   - 为了确保 `master` 分支的稳定性，任何直接修改 `master` 分支的行为都是被严格禁止的。
   - 从 `develop` 分支建立 `release` 分支，可以将发布前的准备工作（例如版本号更新、最终测试等）与 `master` 分支隔离开来，避免意外污染主分支。

2. **持续集成和开发 (Continuous Integration and Development):**
   - `develop` 分支整合了所有已完成的功能分支，代表着最新的开发进度。
   - 当需要发布新版本时，从 `develop` 分支建立 `release` 分支，可以确保新版本包含了所有最新的功能和修复。
   - 如果从 `master` 分支建立 `release` 分支，可能会遗漏 `develop` 分支上已经完成但尚未合并到 `master` 分支的代码。

3. **允许并行开发 (Parallel Development):**
   - 当一个 `release` 分支建立后，开发团队可以继续在 `develop` 分支上开发新的功能，而不会影响到即将发布的版本。
   - 如果从 `master` 分支建立 `release` 分支，那么在发布准备期间，开发团队就无法继续提交新的代码到 `master` 分支，这会阻塞开发进度。

