---  

![[IMG-wemo 內部 DNS 討論-20241003104934159.png]]


目標結果

只有 apollo, user, box 才從外面進來
內部互動都透過 domain name 來互動


domain name 可以用 subdomain
subdomain 也可以切環境


利用 gcp 的 internal DNS 來做


一個 新env 就需要加一個新 domain -> if using one-on-one
qat 上如果要新env, 很輕鬆, gcp 內部直接設定

box svc -> 目前 靠 k8s 的 dns 來做
這樣管理不一致

讓 box svc 通通用 內部 gcp internal dns



未來如果開new micro
need a name
然後去 parse this dns and ingress 設定


k8s 內部互動? -> 不建議自己的 k8s dns管理 -> 建議 用 gcp zone
希望可以寫一個 dns, 不用管 k8s

k8s 內部和跨k8s外面，只要都是內網，都可以透過 name serve record?

cube dns 可以用自管的
可以用 cname 去管內部解析

--

未來 vm 會收掉


如果目前 exising vm, how to talk to k8s?

currently, k8s 內網路互打
如果scope 要拉到全部都用這一套

exisiting k8s 網內互打 -> 只有 box svc

==

KB: 
k8s internal use k8s 內部的寫法用
外面用 zone 的寫法

知識溝通成本比較多? 是短期還是長期?

＝＝

josh

k8s 如果是 devloper , I don't need to know you are vm or k8s

=


jack

會有不一致

box svc 不需要外部 -> internl

box need 內部 dns


box use grpc
我不知道有沒有對外暴露
開發裡面還是外面
我測試才okay, 
上 qat 打不到,
這套不一致

有成本，就是你要知道這是 內部還是外部
可以解跟不可以解的部位

==

KB:
那我們現在是如何開發的
hernes 打 ps

local 可以直接測試，如果用 xxx.prod, xxx. dev....

現在如何開發?

jack

box 解在 k8s內部 
atlas DNS 解
我需要做一個東西跑在 vm上
code 可以呼叫 grpc, 上 qat 打不到
等到他發現, 原來她的 job 在 vm 打不到 grpc
需要把 job 搬到 k8s 才可以打到



==

KB

ps call vs, how to do?

k8s internal -> 網內互打
跨k8s -> 應該要打到 gateway上吧?

jack:
kong 是 api getway
rest 進來才會過 kong

===


過 kong 你還是要用 dns 解，要不要過 kong 這是另一個事情



目前只有左到右
![[IMG-wemo 內部 DNS 討論-20241003104948987.png]]


如果不走 kong,?

how to do?
- discovery svc?
- health check?


不是這題
目前這題是：只需要用 k8s 的 dns or 內網的 dns




1. k8s internal invokation  -> 2 opt
2. VM to k8s 
	- 使用者付款 api 打 hermes, => ceres -> tappay -> hook 過 kong/reverse proxy -> ceres -> hermes
1. k8s to VM
![[IMG-wemo 內部 DNS 討論-20241003105015099.png]]



dodady
![[IMG-wemo 內部 DNS 討論-20241003105041493.png]]




wemo.intra -> gcp  good!
wemo.scooter 管理權 1.1 上 -> 加上 subdomain 需要 去 1.1 ... no good

無法用 subdomain 
![[IMG-wemo 內部 DNS 討論-20241003105041746.png]]


可以設定解出來是 外還是內 ip
![[IMG-wemo 內部 DNS 討論-20241003105104589.png|325]]



自定義
![[IMG-wemo 內部 DNS 討論-20241003105125324.png]]



大家都可以解出這個 private
![[IMG-wemo 內部 DNS 討論-20241003105125439.png]]


然後會繞到外面，再回來

![[IMG-wemo 內部 DNS 討論-20241003105135138.png]]





































