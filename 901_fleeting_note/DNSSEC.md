
- 網域名稱系統安全擴充 (DNSSEC) 是 DNS 的擴充功能
- DNSSEC對資料進行數位簽名，以防止攻擊者操縱或毒害對 DNS 請求的回應。
- 為了確保安全性查找，簽章發生在 DNS 查找過程中的每個層級。因此，來自 DNS 的所有答案都是可信的。這些數位簽章可確保資料不會被竄改。
- 例如，在 `privacyguides.org` 尋找的情況下，根 DNS 伺服器將為 `.org` 名稱伺服器簽署金鑰，然後 `.org` 名稱伺服器將為 `privacyguides.org` 名稱伺服器簽署金鑰
- 但需整個dns cert chain 都是可信的
- 如果 ISP給你搞鬼，還是可以被竄改


PS: 但DNSSEC 不會為 dns查找提供隱私保護

![[IMG-DNSSEC-20250810200234444.png]]


ref:
DNSSEC [https://howdnssec.works/why-do-we-need-dnssec/](https://howdnssec.works/why-do-we-need-dnssec/)