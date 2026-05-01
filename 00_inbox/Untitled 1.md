
```
background 
- The original implementation worked for small and middle size flows, but some query result contain large datasets

Problem
- 10k data took 2.5 min and then error out (env: dev env have 600 mb pod limit cap)

goal
- can handle 100k in stg/prod env

RCA:
- check memory usage
 - check grafana at dev -> see 1.5x over pod mem limit
 - use visualVM to check heap mem usage behavior -> over 600mb
- Data creation pattern
- Excel library behavior (Java Poi) 

Optimization Strategy 
- Use Multi-part form Data 
- reduce intermediate data structures
- Adopt POI Streaming Excel Writer 


- Technique #1 — Use Multi-part form Data at HTTP protocol
    - 因為既有架構限制，數據必須經由 A 服務轉發給 Excel 服務，為了避免 A 服務傳來的巨大 JSON 撐爆 Heap，我們改用 Multi-part 串流傳輸
	- deserialization json string need to store in heap and this cause excel service OOM multi-part form data can put data into tmp-file at disk and we streaming data from those tmp-file
	

- Technique #2 — reduce intermedia object creation
	- 避免將 JSON 轉成巨大的 List<DTO>，而是使用 Jackson Streaming API 搭配後續的 POI SXSSF
	- data is directly streaming to poi from incoming multi-part form data 

- Technique #3 — Streaming Excel Writer: POI:XSS -> POI:SXSSS
	- 原理：它只在記憶體保留 N 列，其餘的 Flush 到磁碟臨時檔，這才是解決 OOM 的核心
	- SXSSFWorkbook.dispose()

- result 
	- 100K at local at JVM 1G limit use visudalVM
	- before and after
	  - 6x speed up
	  - can handle 50k at dev env (600 mb limit cap)
	  - shall be handle 100k at stg/prod  (4g limit cap)


- Considerations for Large Excel Generation 
	- With streaming approach, some features become expensive or difficult to support:
		- Auto-resize Column 
		  - 我們透過預估字元長度來手動設定寬度，或者在前端展示時處理
		- Cell Merge
		  


- future improvement
	- use FastExcel replace POI if we can remove style
	- use csv can even faster
	- app level
	  - 資料一從db出來那邊就用 batch 的方式來處理，然後呼叫 api, 讓這串都是stream的
	  - one producer (mongo query) and one consumer (excel gen)  with flow control / backpressure
	  - review mongo query and optimize it
	- 另外就是我們也可以不需要把 excel 這個服務當成一個 ap, 他可以是一個 llb -> no network cost and 反/序列化 process


      
```

above is the materail I have.
I am going to an backend interview.
and I know this interviewer will going to ask me to desc a chellegne probelm.

given this material, how shall I prepare?
is any logical missing in this material I need to clarify and think it though while I am still at home and be prepare?
what shall I emphasis and put more attention during the interview and what exactly I can prepare in advance for this interview?




---


- not guess, have evident to back up ur word at each conclusion
- know the trade-off
	- ex: auto resize col, cell merge
	- think thru "what if"...
		- what if 還要是做呢？what else you can do, "be prepare"
- how do you position the root cause?
- how can approve it?
- what's ur hypothesis, and how to you verify it and if ti's wrong, what's the next step?
- it's not about u use streaming..
	- it about, why not?
		- 為什麼不用加 memory？
			- it's bruatal way to do, we can do, and let's it make the last resort.
		- 為什麼不用拆 batch？
			- I did use batch
		- 為什麼不用 async job？
		- 為什麼不用 CSV？




shutdown 時：

```
while (activeJobs > 0) {
  await sleep(500);
}
```

👉 這是 **graceful 的核心**

===
I don't understand the logic behind this code.
say the pod will close with 90 sec: 
terminationGracePeriodSeconds: 90  <-- have  this setting in k8s

but at last sec, I have above code, the pod still KILL pod, the sleep(500) is useless?
what sleep 500 purpose here?