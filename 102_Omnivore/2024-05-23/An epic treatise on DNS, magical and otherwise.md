---
id: a376c300-bb58-4d07-8829-093b29aee333
---

# An epic treatise on DNS, magical and otherwise
#Omnivore

[Read on Omnivore](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be)
[Read Original](https://tailscale.com/blog/magicdns-why-name)

## Highlights

> 任何類型的具有靜態時間段的基於輪詢（而不是基於推送）的快取總是會遇到這個問題：您應該選擇什麼快取逾時？如果設定太短，則會增加查詢延遲並使伺服器過載。如果時間太長，更改需要很長時間（有時是幾小時或幾天！）才能傳播。這就是中介機構介入並試圖透過擾亂 TTL 來「解決」問題的原因。但他們最終總是會製造出不同的問題 [⤴️](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be#76afc43b-b939-46f9-a4b2-f67536c82e3e)  ^76afc43b

> 當您的伺服器遍布世界各地並且您希望將使用者定向到最近的伺服器，或忽略當前恰好發生故障的區域時，它就會失敗。因此，營運商已將 DNS 伺服器納入其負載平衡基礎設施，將使用者引導至最近的應用程式伺服器，而不是任何單一正確答案 [⤴️](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be#eee8ae8a-9a89-440d-9eeb-1cab77f411c3)  ^eee8ae8a

>  [⤴️](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be#590defa7-edef-484d-afb8-b672e21be5b9)  ^590defa7

> 將世界各地人們觀察到的 DNS 變更的方式稱為「傳播」是一種常見的誤解。這在技術上是不正確的。您等待的大部分時間是快取過期 [⤴️](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be#1b50de13-fb75-494b-a7d2-23b31e5c5606)  ^1b50de13

> DNS 是一種未加密、未經身份驗證的協定。查詢和回應透過網路上的純文字發送。這意味著任何人或任何事物只需發送正確的名稱即可獲得 IP 位址 [⤴️](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be#21125331-c49c-470f-a9fa-d7e9c1ebfa51)  ^21125331

> DNSSEC is not as good as it sounds. [@tqbf](https://twitter.com/tqbf) has a detailed rant called [Against DNSSEC](https://sockpuppet.org/blog/2015/01/15/against-dnssec/) that systematically refutes every reason you might have for using DNSSEC. Except FedRAMP, I guess. [⤴️](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be#8364f5ac-397c-4baf-bf45-d08700817d69)  ^8364f5ac

> There are new projects like [GNS](https://www.gnunet.org/en/gns.html) that enable end-to-end request privacy, but they have other disadvantages and aren’t very widely deployed. [⤴️](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be#053f2c10-eff7-4a1f-b6e1-9d78156cabca)  ^053f2c10

> DNS 沒有動態加密、準確性或驗證機制。這也意味著無法判斷客戶端是否有權存取給定的 DNS 記錄。沒有本機方法可以建立與 DNS 請求關聯的身分。這意味著更新 DNS 記錄（例如，動態 DNS）無法透過 DNS 本身完成，而必須委託給某種第三方，然後第三方使用非標準化 API。沒有好的 API 可以自動修改 DNS；我們只能容忍 API，因為我們別無選擇 [⤴️](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be#58409bf2-7a0c-49e6-9400-ed939ca4e036)  ^58409bf2

> 當你看到一個大網站癱瘓時，通常是因為 DNS 伺服器再次崩潰而導致所有內容都癱瘓了。當您的私人內部網路表現異常或緩慢時，通常是本地 DNS 故障（或舊的快取值，或節點之間的 DNS 配置不匹配，或由於月相而影響海底光纜的潮汐力 [⤴️](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be#dffc54e5-d34e-4c6c-bd22-3428df235dc3)  ^dffc54e5

> MagicDNS 透過在尾網上的每台電腦上的虛擬位址 `100.100.100.100` 本地運行 MagicDNS 伺服器來修復此問題。 DNS 伺服器不能宕機。它不會因負載而翻倒（除非您自己的機器也會翻倒），當您的機器因某種原因翻倒時，其他所有機器都不會受到影響。 [⤴️](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be#50f4fe14-d7c2-4429-81ee-c194eb46ffa9)  ^50f4fe14

> 由於 MagicDNS 始終在本地運行，因此您甚至不需要信任端對端加密：MagicDNS 流量永遠不會離開您的電腦。它是虛擬網路上的虛擬服務 [⤴️](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be#1c590f03-0ec7-4804-9574-573d06874881)  ^1c590f03

> MagicDNS 對 Tailscale 特定的 DNS 名稱使用委派，但所有委派都發生在您自己的機器內部，這意味著委派延遲實際上為零 [⤴️](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be#b6fdfbf2-a9c2-4841-bd1c-688fa7e72b98)  ^b6fdfbf2

> MagicDNS 可以將盡可能多的 DNS 查詢透明地升級為 DNS-over-HTTPS，以便向外界發送的 DNS 請求不會在傳輸過程中被篡改或嗅探 [⤴️](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be#efda01fc-41ff-47cc-a2f5-f5547f311738)  ^efda01fc

> 如果您建立拆分 DNS 路由，則此本機 DNS 伺服器也可以委派子網域，即使您的作業系統本身不支援該功能。當您在管理控制台中設定拆分 DNS 時，這些路由將自動推送到尾網路中的所有設備，並允許您根據需要將流量路由到任何子網域或虛擬頂級網域。例如，您也可以使用它從非 AWS Tailscale 節點存取 AWS VPC 網域。即使在 Windows 7 上也是如此 [⤴️](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be#e2573670-74ca-4b78-b99d-9f8f74819395)  ^e2573670

> MagicDNS 完全解決了快取失效問題，因為我們的控制平面會立即將更新推送到每個設備，其中 DNS 會定期輪詢變更。由於它直接在您的裝置上運行，因此我們消除了中間件弄亂快取參數的可能性。這意味著您可以相信您的內部 DNS 現在始終是最新的，而不必擔心配置另一個內部 DNS TTL [⤴️](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be#cdf1762c-d921-434b-9214-19e8854845f6)  ^cdf1762c

> 但是，如果不像 MagicDNS 那樣在每台電腦上運行 DNS 伺服器，更新延遲就會再次成為問題。安全性和快取失效再次成為問題。 DNS 伺服器的正常運作時間和負載成為一個問題。它成為一個失敗點，而不是一個復原點 [⤴️](https://omnivore.app/me/an-epic-treatise-on-dns-magical-and-otherwise-18fa31063be#284e7726-48f3-4cf1-96c7-2f67c8836b2a)  ^284e7726

