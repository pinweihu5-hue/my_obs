
[[backend_idx]]


- [https://github.com/sindresorhus/got/tree/main/documentation](https://github.com/sindresorhus/got/tree/main/documentation)
- [https://github.com/sindresorhus/got/blob/main/documentation/lets-make-a-plugin.md](https://github.com/sindresorhus/got/blob/main/documentation/lets-make-a-plugin.md)


- 需要有 retry 機制嗎？ 多複雜的機制?
- timeout 設定，最好是要有，不要一直 hang 住
- 如果 http 拿的內容都短期不會變化且量大(或是未來會量大)，用 [cache](https://github.com/sindresorhus/got/blob/main/documentation/cache.md)
- 看是否需要設定 特定的 user agent  → 方便識別
- 如果需要 per-user basis 的 state 跨 request 傳遞 → cookie and session
- 有些 api 需要設定特別的 header 欄位，像是 github 需要 設定 header.accept = `application/vnd.github.v3+json`, 我印象 kafka 也需要設定
- 如果返回的東西固定都是 json, 有些套件， like, Got 可以透過設定就幫你 parse  好，或是如果你用比較簡單的 http client,  那你就要自己 parse
- 通常都需要帶上 auth 的 token
- 回來的東西有多少？會很多的可能的話，需要有pagination support
- 如果回來的東西需要寫入另一個東西 ? then can consider use stream to increase perf
	- like write into file?
	- like write into another network socket
- error msg 要接住，要可以正常的顯示出來
	- 有些 api 如果有設定rate limit, 那 error need to handle and can identify this is rate limit error
- do we need to http support
- do we need to use proxy? [see](https://github.com/sindresorhus/got/blob/main/documentation/tips.md#proxying)
	- to bypass firewall
	- to 匿名
	- to bypass geolimit restriction
	- to modify something in proxy server
	- to just proxy to another server(to actually do work), and the reason to hit to the current server is cuz current server is a proxy layer, for now
		- like hermes proxy to reconciliation system
- note: incoming http msg as readable stream and and, say, you write into anywhere/file, that is a writeable stream