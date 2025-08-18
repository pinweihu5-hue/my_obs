[[wemo_vip_lastPayAt.excalidraw]]
![[IMG-wemo pass lastPayAt and nextChargeAt-20241003104934085.png]]
nextChargeAt 下一次付款, 續訂成功押上
lastPayAt 扣款時間

如果續訂成功，資料面都會是 nextChargeAt > lastPayAt 
如果續訂失敗，nextChargeAt < lastPayAt, 表示 lastPayAt 已經押上，但是 nextChargeAt 因為沒有續訂成功，沒有押上
![[IMG-wemo pass lastPayAt and nextChargeAt-20241003104948943.png]]
