

wemo meeting 2024_10_24-15_02_28

這個需求目前只需針對FLY的車種

透過機車的can bus 讀取到光陽吐出的error code，
當收到error code時排除現有已知的對應故障訊息外
其餘的統一先上一個Maintenance Item item中文名稱為Fiy 故障碼(非下線item)。

需提供對應工具或是方式給FAE可以查找收到的error code 資訊


1. fae 出現 ! -> record err code

BB can receive these err
fae wana to have a palce to find those err code, the timing of thosr errc deo

2. when err code happen -> use 1 main-item to represent that


task
1. add main-item
2. how to send those error code to server and record that and allow FAE to check it
3. volumn is a lot -> can not save into db, can be a log, maybe put bigQuery, just a place to let fae to search
4. how to lookup?
	1. scooter id
	2. time
	3. by content
5. a way to monitor the detail behavor for a scooter
6. once we know more info and then we will add mo


below is the data in SI db, currrently only candy
use cloud function to write into 
![[IMG-wemo meeting 2024_10_24-15_02_28 - collect error code-20250810201310941.png]]

no need candy
only need i-one

jean hope goes into biquery

save data (from current to at least "many" yr)


