


## Alex
### 常見目的
- search engine indexing
- Web archiving
- Web mining (現在 AI 更需要)
- Web monitoring (看看有沒有盜版等侵權)


### 基本需求
- 1 month collect 1 billion pages
- no duplicate
- html only
- also consider updated or newly added pages
- store pages up to 5 years

### 非功能需求
- ez to scale more
- avoid web strap, bad html, malicious links
- Politeness



### High level design
![[IMG-web crawler 網路爬蟲-20250810200244628.png]]

### 元件

- 種子 url
	- 怎麼挑需要思考(ai)
- url frontier: 
	- 準備要下載的 urls, from seedURL and from URL frontier
- html downloader: 
	- 功能就是 downloads web pages
- DNS Resolver: 
	- 專責 DNS 服務來處理，因為量很大
	- 量很大，基本上這邊可能還需要自己搞 local DNS cache
- Content Parser: 抓下來後需要 parse, 和過濾有問題內容, 不需要內容等
- Content Seen?
	- 約 1/3 的 html at internet 是重複的，compare via hash digest
		- 有重複 -> discard
		- 沒有重複 進入 URL Extractor
- Content Storage
	- 要快的話，可以多一層 mem
	- 需要夠大的話，放 s3 之類的服務
- URL Extractor
	- 從 html page extract link and passed to URL filter
- URL filter
	- 移除一些不需要的 ext, 內容， error link, 加入黑名單的 url -> 傳入 URL Seen?
- URL Seen?
	- 如果一定時間內抓過，或是已經在 URL storage，那就可以濾掉
	- 如果還沒抓過 -> 進入 url frontier
- URL Storage
	- 把準備要抓的url 都放在這邊


### Design deep dive
- BFS or DFS?
	- dfs 不適合，因為會走太深, 通常會用 bfs
	- bfs 會遇到兩個問題
		- 一直抓同一個網站 -> impolite -> 被 block
		- 沒有 priority 機制
	- 這邊用 URL frontier 解決
		- 我們用很多 queue, 每一個 queue 都是一個特定的 domain
		- worker 每一次只會從 一個 q 拿出來處理，一個 q 不可以被太多 worker 同時處理 -> 來解決 impolite
		- 沒有 priority 機制 -> 我們也可以根據不同的 pr, 來建立很多 queue, 我們讓 worker 去 高 pr queue 拿 task 的機率比較大
![[IMG-web crawler 網路爬蟲-20250810200246002.png|818]]


- Robots.txt
	- HTML Downloader 抓到這個檔案，要遵守其規定
- 優化
	- HTML Downloader 開並行, make it stateless, use serverless runtime, like lambda
	- DNS 很多機制是同步的，可能會是瓶頸。
		- 利用 DNS cache
	- locality: 讓 HTML Downloader 跟要抓的 website 是同一個區域
	- timeout: 如果一直抓不到，短一點的 timeout 可以提昇效率



## link
[1] US Library of Congress: https://www.loc.gov/websites/
[2] EU Web Archive: http://data.europa.eu/webarchive
[3] Digimarc: https://www.digimarc.com/products/digimarc-services/piracy-intelligence
[4] Heydon A., Najork M. Mercator: A scalable, extensible web crawler World Wide Web, 2 (4) (1999), pp. 219-229
[5] By Christopher Olston, Marc Najork: Web Crawling.
http://infolab.stanford.edu/~olston/publications/crawling_survey.pdf
[6] 29% Of Sites Face Duplicate Content Issues: https://tinyurl.com/y6tmh55y
[7] Rabin M.O., et al. Fingerprinting by random polynomials Center for Research in Computing Techn., Aiken Computation Laboratory, Univ. (1981)
[8] B. H. Bloom, “Space/time trade-offs in hash coding with allowable errors,” Communications of the ACM, vol. 13, no. 7, pp. 422–426, 1970.
[9] Donald J. Patterson, Web Crawling: https://www.ics.uci.edu/~lopes/teaching/cs221W12/slides/Lecture05.pdf
[10] L. Page, S. Brin, R. Motwani, and T. Winograd, “The PageRank citation
ranking: Bringing order to the web,” Technical Report, Stanford University,
1998.
[11] Burton Bloom. Space/time trade-offs in hash coding with allowable errors. Communications of the ACM, 13(7), pages 422--426, July 1970.
[12] Google Dynamic Rendering:
https://developers.google.com/search/docs/guides/dynamic-rendering
[13] T. Urvoy, T. Lavergne, and P. Filoche, “Tracking web spam with hidden style similarity,” in Proceedings of the 2nd International Workshop on Adversarial Information Retrieval on the Web, 2006.
[14] H.-T. Lee, D. Leonard, X. Wang, and D. Loguinov, “IRLbot: Scaling to 6 billion pages and beyond,” in Proceedings of the 17th International World Wide Web Conference, 2008.