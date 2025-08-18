[[tunnel_idx]]
[[network_idx]]
https://www.cloudflare.com/zh-tw/learning/network-layer/what-is-tunneling/


# What it is
- 用在網路協定不支援時，你可以把你要傳的東西，封裝在支援的封包內，就可以傳了
- 通常會加密
- 通常用在 vpn, 這樣可以通過網路本身不支援的協定的環境
- 封包由 header  和 payload 組成
	- header 包括要去哪裡，和用什麼protocol
	- payload 就是內容


# 封裝
封裝的意思：
就是把另一個 head + payload 變成外層的payload,  然後外層就會加上自己的 header


架設 a <-> b 兩地目前只有支援 ipv6
但是 a, b 裡面又有一部分只能使用 ipv4
使用封裝，我可以把 ipv4 封裝在 ipv6 內，這樣就可以了
因此可以分為 payload protocol(負載的協定) and delivery protocol(傳的協定)

封裝也可以改變你傳輸的真實意圖
- 一方面是透過加密
- 一方面可能是我會檔掉某個網路協議，但是允許某個網路協議，那你就封裝成允許的那個網路協議 -> 因此用來規避 fw rule

更多協定看 wiki:  https://en.wikipedia.org/wiki/Tunneling_protocol











