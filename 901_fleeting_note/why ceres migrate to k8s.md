1. we want HA
2. disk space problem → also need to setup log rotation
3. lower cost? => we can shut down this vm
4. 把東西盡量都用 k8s 管理
5. 還有一些其他服務放在 ceres 的那台 VM 上，因此還要賣賣搬
    1. pluto(信託處理)
    2. janus(mengo)
    3. 對帳系統(reconciliation, 上傳3-4個報表，寄信給財務?..打api問ceries, 沒有連db=>可以拆成 service 放 k8s，一週用一次)



