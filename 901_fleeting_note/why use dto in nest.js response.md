

改用 dto


dto 的壞處
- 沒辦法像是 interface 可以 pick, mit, 復用比較低

但是好處是，可以封裝你的資料結構
封裝後，轉換資料可以不用混在 svc 層，把資料轉換讓 dto 那邊處理
你的 svc 層的 code 會更簡潔


diff
![[IMG-why use dto in nest.js response-20250807202923674.png|585]]
![[IMG-why use dto in nest.js response-20250807202925088.png|591]]


![[IMG-why use dto in nest.js response-20250807202926526.png|859]]