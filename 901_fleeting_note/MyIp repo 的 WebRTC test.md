[[How NAT traversal works]]
[[source code reading]]


---



link: 
- [GitHub - jason5ng32/MyIP: The best IP Toolbox](https://github.com/jason5ng32/MyIP)
- [IPCheck.ing](https://ipcheck.ing/#/dnsresolver)
- [WebRTC 入門教學（二）：以 RTCPeerConnection 建立 Peer-to-peer 連線 – G. T. Wang](https://blog.gtwang.org/web-development/webrtc-peer-connection/)



如何檢測的?
![[IMG-MyIp repo 的 WebRTC test-20250811203950769.png]]




主要是透過函數
```ts

// 测试 STUN 服务器
const checkSTUNServer = async (stun) => {
  return new Promise((resolve, reject) => {
    try {
      // ice 的 config 
      // ice is a tech to us TURN and STUN to find candidate(ip, port...network config ) to enable p2p connnstion 
      const ICEconfig = { iceServers: [{ urls: 'stun:' + stun.url }] };
      const peerConnection = new RTCPeerConnection(ICEconfig);

      // 下面timeout 用的
      let candidateReceived = false;


      // 等待找到 candidate 的事件監聽器
      peerConnection.onicecandidate = (event) => {
  
        if (event.candidate) {
          candidateReceived = true;
          const candidate = event.candidate.candidate;
          const ipMatch = /([0-9a-f]{1,4}(:[0-9a-f]{1,4}){7}|[0-9a-f]{0,4}(:[0-9a-f]{1,4}){0,6}::[0-9a-f]{0,4}|::[0-9a-f]{1,4}(:[0-9a-f]{1,4}){0,6}|[0-9]{1,3}(\.[0-9]{1,3}){3})/i.exec(candidate);
          if (ipMatch) {
            stun.ip = ipMatch[0];
            IPArray.value = [...IPArray.value, stun.ip];
            stun.natType = determineNATType(candidate);  // <---這一段
            peerConnection.close();
            resolve();  // 成功时解析 Promise
          }
        }
      };

      peerConnection.createDataChannel("");
      peerConnection.createOffer().then((offer) => peerConnection.setLocalDescription(offer));

      // 设置一个超时计时器来拒绝 Promise
      setTimeout(() => {
        if (!candidateReceived) {
          peerConnection.close();
          reject(new Error("Stun Server Test Timeout"));
        }
      }, 5000);
    } catch (error) {
      console.error("STUN Server Test Error:", error);
      stun.ip = t('webrtc.StatusError');
      reject(error);  // 捕获异常时拒绝 Promise
    }
  });
};






// 分析ICE候选信息，推断NAT类型
const determineNATType = (candidate) => {
  const parts = candidate.split(' ');
  const type = parts[7];

  if (type === 'host') {
    return t('webrtc.NATType.host');
  } else if (type === 'srflx') {  // srflx is `server reflexive candidate` 是一種優先用來實現穿越 NAT 和防火牆的候選者位址。這些候選者位址是通過 STUN 伺服器獲得的
    return t('webrtc.NATType.srflx');
  } else if (type === 'prflx') {
    return t('webrtc.NATType.prflx');
  } else if (type === 'relay') {
    return t('webrtc.NATType.relay');
  } else {
    return t('webrtc.NATType.unknown');
  }
};




```


```
  本地主機 (Host)
    |
 (私有 IP)
    |
   NAT
    |
 (公共 IP) <-----> STUN 伺服器
                     |
                 (Public IP:Port)
```

當一個設備在 NAT 內部，並且試圖建立與另一設備的點對點連接時，它會先向 STUN 伺服器發送一次請求，STUN 伺服器回應這個請求並告訴設備它的公共 IP 和通訊埠。這就是 `server reflexive candidate`。設備會把這個公共 IP 和通訊埠發送給對方，用來建立點對點通訊連接。


stun server 來自
```ts

const stunServers = reactive([
  {
    id: "google",
    name: "Google",
    url: "stun.l.google.com:19302",
    ip: t('webrtc.StatusWait'),
    natType: t('webrtc.StatusWait'),
  },
  {
    id: "nextcloud",
    name: "NextCloud (QUIC)",
    url: "stun.nextcloud.com:443",
    ip: t('webrtc.StatusWait'),
    natType: t('webrtc.StatusWait'),
  },
  {
    id: "twilio",
    name: "Twilio",
    url: "global.stun.twilio.com",
    ip: t('webrtc.StatusWait'),
    natType: t('webrtc.StatusWait'),
  },
  {
    id: "cloudflare",
    name: "Cloudflare",
    url: "stun.cloudflare.com",
    ip: t('webrtc.StatusWait'),
    natType: t('webrtc.StatusWait'),
  },
]);



```