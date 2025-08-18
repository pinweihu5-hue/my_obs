

1. 最單純的，只有 singletonObject 會循環依賴
![[IMG-spring solve circular dependency-20250214082113499.png]]



2. 沒有代理的情境, 使用 singletonFactories 放半成品，即可解決circular dependcy 問題
![[IMG-spring solve circular dependency-20250222193846541.png]]



3. 當我們有代理，使用 singletonFactories 放半成品，無法解決circular dependcy 問題。因為b 在依賴注入的時候，注入的是實際的a, 但是我們需要注入的是proxy的a
![[IMG-spring solve circular dependency-20250807203858075.png]]



4. 多引入 earlySingletonOjects解決代理下的循環依賴問題
	1. 當a getBean時，newA 後，會先放入 singletonFactories (放入的是 fa)。fa 是一個 factory, 可以 ret a 也可以是 proxy of a (pa)
	2. 然後 setB, 依賴注入 b, 找不到，開始進行 b 的實例化 getBean for b
	3. b 也一樣, new B 後，先放入 singletonFactories (放入的是 fb)
	4. b 要倚賴注入a時，去 singletonFactories找，找到 fa, fa 給 b proxya, 讓 b 拿到依賴注入
	5. fa 也會把 pa 放入 earlySinletonObjects
	6. 然後 b 進行 proxy enchance, 變成 pb
		1. 會檢查是否有已經被增強過，這邊沒有，因此 b 要 proxy 變成 pb
	7. 最後 b 完成全部 init, 最後是 pb, 放入 singletonObject
	8. 然後又回到 a, a 可以拿到 pb了，a 可以完成依賴注入了
	9. . 最後 a 進行 proxy, 檢查是否有被增強過，去 earlySinletonObjects 看，有！因此 a 不用增強了
	10. 最後 pa 結束，放入 singletonObject
	11. done
![[IMG-spring solve circular dependency-20250807203859478.png]]
