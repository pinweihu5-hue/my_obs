 ---  
alias:  
creation_date: 2024-05-27 19:34  
tags: 
related:[[linux_idx]]
---


先 gen pri pub key

ssh-keygen -t rsa -f ~/.ssh/[KEY_FILENAME] -C [gcp_USERNAME]
gcp_USERNAME 可以先登入，看這裡
![[IMG-ssh 設定 in gcp-20241003104934048.png]]


ssh-keygen -t rsa -f ~/.ssh/p28152133_01 -C p28152133

gen 好後，copy pub key
`cat /Users/re4388/.ssh/gcp_20240406-011050.pub`



pub key 放在這裡
![[IMG-ssh 設定 in gcp-20241003104948930.png]]


貼上
![[IMG-ssh 設定 in gcp-20241003105010698.png]]

![[IMG-ssh 設定 in gcp-20250308075956883.png]]




如果就可以用下面方式登入了
```sh


ssh -i path-to-private-key username@ip

ssh -i /Users/re4388/.ssh/p28152133_01 p28152133@34.80.152.145

```


也可以在 ~/.ssh/config 設定別名，可以更方便登入 like 

`gcp01`

設定方法如下
```sh

Host gcpApache01            //自訂alias
HostName 14.19.17.13         //ip
Port 22                     //host SSH port
IdentitiesOnly yes          //指定key   
IdentityFile ~/.ssh/gcpa01  // 指定pub key 路徑
User gcpa01                 //登入username，


實際例子

Host gcp20240406-011050
  User re4388_103_0406
  HostName 34.80.127.242
  Port 22
  IdentitiesOnly yes
  IdentityFile ~/.ssh/gcp_20240406-011050


```





ref:
https://ithelp.ithome.com.tw/m/articles/10251134
