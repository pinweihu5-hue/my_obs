---  
alias:  
creation_date: 2024-06-09 16:05  
tags: 
related:
- [[source code reading]]
- [[dns_idx]]
- [[不良林-代理进阶篇]]

---  



透過打 edns or extend-dns 的相關服務去看看你的 dns 的地理位置和你代理地理位置是否一樣和是否不同
如果你用vpn掛日本代理，但是你的 dns 有leak 出不同的地理(你的 dns query 可能沒走代理 XD)，可以合理懷疑你有掛代理且你有 dns leak

類似netflix 就可以透過 dns leak 來抓代理
或是 GFW 可以判斷你去哪裡然後檔掉，或是竄改dns結果

[GitHub - jason5ng32/MyIP](https://github.com/jason5ng32/MyIP)
```ts



// DNS 泄露测试 1
const fetchLeakTestIpApiCom = (index) => {
  return new Promise((resolve, reject) => {
    const urlString = generate32DigitString();

    // `edns.ip-api.com` 是一個提供擴展 DNS （EDNS）服務的網站，它的主要功能是提供基於 IP 位址的地理定位資訊
    const url = `https://${urlString}.edns.ip-api.com/json`;

    fetch(url)
      .then((response) => {
        if (!response.ok) {
          throw new Error("Network response was not ok");
        }
        return response.json();
      })
      .then((data) => {
        if (data.dns && "geo" in data.dns && "ip" in data.dns) {
          const geoSplit = data.dns.geo.split(" - ");
          leakTest[index].country_code = countryLookup.byCountry(geoSplit[0]).iso2;
          leakTest[index].country = getCountryName(leakTest[index].country_code, lang.value);
          leakTest[index].ip = data.dns.ip;
          resolve();
        } else {
          console.error("Unexpected data structure:", data);
          reject(new Error("Unexpected data structure"));
        }
      })
      .catch((error) => {
        console.error("Error fetching leak test data:", error);
        leakTest[index].country = t('dnsleaktest.StatusError');
        leakTest[index].country_code = t('dnsleaktest.StatusError');
        leakTest[index].ip = t('dnsleaktest.StatusError');
        reject(error);
      });
  });
};



// DNS 泄露测试 2
const fetchLeakTestSfSharkCom = (index, key) => {
  return new Promise((resolve, reject) => {
    const urlString = generate14DigitString();
    const url = `https://${urlString}.ipv4.surfsharkdns.com`;

    fetch(url)
      .then((response) => {
        if (!response.ok) {
          throw new Error("Network response was not ok");
        }
        return response.json();
      })
      .then((data) => {
        const getKey = Object.keys(data)[key];
        const keyEntry = data[getKey];

        if (keyEntry && keyEntry.CountryCode && keyEntry.IP) {
          leakTest[index].country_code = keyEntry.CountryCode;
          leakTest[index].country = getCountryName(keyEntry.CountryCode, lang.value);
          leakTest[index].ip = keyEntry.IP;
          resolve();
        } else {
          console.error("Unexpected data structure:", data);
          reject(new Error("Unexpected data structure"));
        }
      })
      .catch((error) => {
        console.error("Error fetching leak test data:", error);
        leakTest[index].geo = t('dnsleaktest.StatusError');
        leakTest[index].ip = t('dnsleaktest.StatusError');
        reject(error);
      });
  });
};


```