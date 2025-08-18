- sql 本身 instance 的 qeury timeout 怕改了出大問題，先不動。
- use pg bouncer
- pg bouncer has connection pool 
- long query -> 可以透過 pg bouncer 的 config 可以設定 timeout
	- db perf issue drop 

![[CleanShot 2024-11-15 at 15.33.48.png|544]]




- 統一入口
![[CleanShot 2024-11-15 at 15.31.29.png|632]]




oldest tx by age 會拉很長, like 7~8 hr
![[CleanShot 2024-11-15 at 15.35.11.png]]


max client con 太低又不行，因此拉高 
query timeout 調低
![[CleanShot 2024-11-15 at 15.36.39.png|636]]



update box msg 當初 10 sec 的 auto vaccum
現在都好很多了

![[CleanShot 2024-11-15 at 15.39.57.png]]