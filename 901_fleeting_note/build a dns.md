[[dns_idx]]

---


[jvns/_dns_-_weekend_](https://github.com/jvns/dns-weekend)  的其他語言實作
[Repository search results · GitHub](https://github.com/search?q=dns+weekend&type=repositories)



Go 的 impl: [GitHub - ba11b0y/godig: A toy DNS resolver inspired from Julia Evan's DNS in a weekend](https://github.com/ba11b0y/godig)



why learn? my use case?


---

[GitHub - morinokami/dns-weekend-deno: https://implement-dns.wizardzines.com/ translated into Deno.](https://github.com/morinokami/dns-weekend-deno)

send to NS 

use udp protocol, 53
這邊 ask 8.8.8.8 

send 應該要送的

回來的response 要 parse



這是用 deno, 因此 bun or node 可能會有點不同



最重要的就是這個resolve


體現了 一層一層遞迴呼叫最後拿到 domain  ip 的過程
這邊我們提供的 ip 198.41.0.4 是 root ns
然後一層一層找到我們要的 domain name 的 ip


我們目的是要知道 google.com 的 ip

1. 先問 root ns (198.41.0.4)
2. 拿到  `l.gtld-servers.net` 的 ip  192.41.162.30
3. 然後用  192.41.162.30 問下一層 ns,  `ns2.google.com`
4. 然後最後 `ns2.google.com` 裡面就有 google.com 的 ip -> 最後拿到 142.251.42.238
 
```ts

/**
 *
 * @param domainNameToAskIp 要查詢的域名, 例如 benhu.com
 * @param recordType 要查詢的 DNS 資料類型, 例如 TYPE_A 表示 A 記錄
 * @returns 返回查詢的 IP 地址
 */
export async function resolve(
  domainNameToAskIp: string,
  recordType: number
): Promise<string> {
  // IP地址 `198.41.0.4` 是一個非常特殊和重要的地址，因為它是服務於域名系統 (DNS) 的根名稱伺服器之一。
  // 具體來說，這個IP地址對應的是名為 "A" 根服務器（Root Server）的公共IP地址。
  // 在全球共有13組根名稱伺服器，命名為A、B、C到M。這些根名稱伺服器的責任是為DNS查詢提供頂點的解析服務，也就是說，它們指向頂級域（TLD）伺服器，比如 `.com`、`.org`、`.net` 等。
  let nameserverIP = "198.41.0.4";
  while (true) {
    console.log(`Querying ${nameserverIP} for ${domainNameToAskIp}`);

    const response = await sendQuery(
      nameserverIP,
      domainNameToAskIp,
      recordType
    );
    const ip = getAnswerForIp(response);
    console.log("ip -->", ip);
    const nsIP = getNameserverIp(response);
    console.log("nsIP -->", nsIP);
    const nsDomain = getNameserver(response);
    console.log("nsDomain -->", nsDomain);

    if (ip) {
      // 如果拿到 IP 就直接返回
      return ip;
    } else if (nsIP) {
      // 如果拿到 NS 的 IP 就更新 nameserver 并回到 while 循环 繼續查詢
      nameserverIP = nsIP;
    } else if (nsDomain) {
      // 如果拿到 NS 的域名呼叫 resolve 函式 來拿到 NS 的 IP
      nameserverIP = await resolve(nsDomain, TYPE_A);
    } else {
      throw new Error("Something went wrong");
    }
  }

  /**
   * 以下是呼叫的 log 輸出: 
   * 
   * Querying 198.41.0.4 for google.com
   * 
   * 第1個 while 循環:
    ip --> 
    nsIP --> 192.41.162.30
    nsDomain --> l.gtld-servers.net
    Querying 192.41.162.30 for google.com


    * 第2個 while 循環:
    ip --> 
    nsIP --> 216.239.34.10
    nsDomain --> ns2.google.com
    Querying 216.239.34.10 for google.com


    * 第3個 while 循環:
    ip --> 142.251.42.238
    nsIP --> 
    nsDomain --> 
    142.251.42.238
   */
}


```



send query

有關如何建立 udp socket
use it to `send` and `receive
```ts


async function sendQuery(
  ipAddress: string,
  domainName: string,
  recordType: number
): Promise<DNSPacket> {
  const query = buildQuery(domainName, recordType);

  // create a UDP socket
  const socket = Deno.listenDatagram({
    hostname: "0.0.0.0",
    port: 0,
    transport: "udp",
  });

  // send query, w/ port 53 is the DNS default port.
  socket.send(query, { hostname: ipAddress, port: 53, transport: "udp" });

  const [data, _] = await socket.receive();
  socket.close();
  return parseDNSPacket(data);
}



```




parse 是苦工 XD
先沒細看了... XD


最後 parse 好後的 response 裡面拿到 ip, ns的 ip, ns 的 domain
```ts


function getAnswerForIp(packet: DNSPacket): string {
  for (const answer of packet.answers) {
    if (answer.type_ === TYPE_A) {
      return answer.data as string;
    }
  }
  return "";
}

function getNameserverIp(packet: DNSPacket): string {
  // name server's ip address is at additionals record
  for (const additional of packet.additionals) {
    if (additional.type_ === TYPE_A) {
      return additional.data as string;
    }
  }
  return "";
}

function getNameserver(packet: DNSPacket): string {
  // name server's domain name is at authorities record
  for (const answer of packet.authorities) {
    if (answer.type_ === TYPE_NS) {
      // need to use TextDecoder to decode the domain name from Uint8Array to string
      const decoder = new TextDecoder("utf-8");
      return decoder.decode(answer.data as Uint8Array);
    }
  }
  return "";
}


```










# old note


- send query to NS(name server)
- local will setup a udp sokcet to send and recevie
- send 之前，需要 build query
	- 主要由 dns header and dns question 組成
- send query to 8.8.8.8, using udp with 53 port
- 拿到的 response 要 prase
	- 從 header 解出 `numQuestions,` `numAnswers,` `numAuthorities,` `numAdditionals`
- 一個 packet 的組成
```ts
class DNSPacket {
  constructor(
	public header: DNSHeader,
	public questions: DNSQuestion[],
	public answers: DNSRecord[],
	public authorities: DNSRecord[],
	public additionals: DNSRecord[],
  ) {}
}

class DNSHeader {
  constructor(
	public id: number,
	public flags: number,
	public numQuestions: number = 0,
	public numAnswers: number = 0,
	public numAuthorities: number = 0,
	public numAdditionals: number = 0,
  ) {}
}

class DNSQuestion {
  constructor(
	public name: Uint8Array,
	public type_: number,
	public class_: number,
  ) {}
}

/**
 * DNSRecord
 *
 * name: the domain name
 * type_: A, AAAA, MX, NS, TXT, etc (encoded as an integer)
 * class: always the same (1). We’ll ignore this.
 * ttl: how long to cache the query for. We’ll ignore this.
 * data: the record’s content, like the IP address.
 */
class DNSRecord {
  constructor(
	public name: Uint8Array,
	public type_: number,
	public class_: number,
	public ttl: number,
	public data: Uint8Array | string,
  ) {}
}
```
        
