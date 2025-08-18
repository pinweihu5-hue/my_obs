---  
alias:  
creation_date: 2024-05-26 19:37  
tags: 
related: [[tunnel_idx]], [[source code reading]]
---  



[punchmole/README.md 位於 main · degola/punchmole --- punchmole/README.md at main · degola/punchmole](https://github.com/degola/punchmole/blob/main/README.md)




```sh

npm install -g punchmole

# server side
PORT=10000 API_KEYS=api-key1,api-key2,random-string-nobody-can-guess PUNCHMOLE_ENDPOINT_URL_PATH=/_punchmole punchmole-server




# client side
PUNCHMOLE_ENDPOINT_URL=ws://34.80.222.226:10000/_punchmole \
TARGET_URL=http://localhost:3000 \
PUNCHMOLE_API_KEY=api-key1 \
DOMAIN=re4388.buzz  \
punchmole




```




弄不起來，不確定哪裡有問題