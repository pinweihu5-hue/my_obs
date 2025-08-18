- [[_wemo_tickets]]
- [[wemo_pass_note]]


---


[BE design doc](https://docs.google.com/document/d/1KTO1eb7S-dbZXWPRgt8q4DyKaSysgeD9BOLxzlNmcDE/edit)

# tmp



現在會自動回收 rebate voucher 嗎? yes

since apollo also need to let mgmt to set  0-plan(0p)
one way is to add a col to denote this record is 0p
will need to modify the API (currently allow user to cancle the conti-sub),
this api need to have if-else to know if this is 0p record
and if this is 0p, we will need to cancel all benefit this user right now, and take back rebate voucher provided

- [ ] if disable 0p, if user enable it again, can he get back the above cancel benefit? -> I gess not, and I also guess make it now also make BE work less















---

# 專案文件

1. [[PRD] WeMo PASS 留存計畫 PHASE 1 - 取消 0 元方案，回收剩餘權益](https://docs.google.com/document/d/1QZhsefyi4hjGkTFc_Jc1N7wITqMxOzFQ0UM9TerRbTo/edit)

# 專案名稱

WeMo PASS 留存計畫 PHASE 1 - 取消 0 元方案，回收剩餘權益（Project Brief: 2024/6/6）

# 連結傳送門

- 專案主票：[[Feature][WPASS] 取消 0 元方案，回收剩餘權益](https://app.asana.com/0/1205309502170758/1206532233236380/f)
- FLOW: [取消 0 元方案的流程](https://www.figma.com/board/p2YTRUJ9qDb9brbrPgzoGY/%E5%85%8C%E6%8F%9B%E5%BA%8F%E8%99%9F?node-id=1709-10168&t=n6GEHZZfEH9jU7LJ-0)
- User App - UI MOCKUPS: [取消 0 元方案的 UI 畫面](https://www.figma.com/design/w7lOyqJcGXZhC1xpPUxiiq/WeMo-App?node-id=16414-37772&t=CnZ5JnT4QjrFANV3-0)
- Apollo - UI MOCKUPS: [設定體驗方案的 Apollo UI 畫面](https://www.figma.com/design/8GORW3KqwUKkyB1QrXCMf7/Apollo?node-id=9657-35&t=K6kMtqmEOYGctRuY-0)

# User Story

## UIUX

作為一個 UIUX 團隊成員，我希望...

1. 用戶在取消 0 元方案時，會看到 [[取消續訂再確認](https://www.figma.com/design/w7lOyqJcGXZhC1xpPUxiiq/WeMo-App?node-id=16429-14458&t=zEPMjnwov559AGOC-0)] 彈窗，若按下「確認」按鈕，畫面回到 WeMo PASS 頁，並顯示 [[取消完成](https://www.figma.com/design/w7lOyqJcGXZhC1xpPUxiiq/WeMo-App?node-id=22107-802&t=zEPMjnwov559AGOC-0)] Toast
2. 用戶在取消 0 元方案時，會看到 [[取消續訂再確認](https://www.figma.com/design/w7lOyqJcGXZhC1xpPUxiiq/WeMo-App?node-id=16429-14458&t=zEPMjnwov559AGOC-0)] 彈窗，若按下「我再想想」按鈕，關閉彈窗，畫面停在訂閱管理頁
3. 當 [[取消完成](https://www.figma.com/design/w7lOyqJcGXZhC1xpPUxiiq/WeMo-App?node-id=22107-802&t=zEPMjnwov559AGOC-0)] Toast 顯示時，顯示 4 秒會自動消失

## IMC

作為一個 IMC 團隊成員，我希望...

1. 用戶成功取消 0 元方案時，系統立刻移除該用戶的 WeMo PASS 會員身份，用戶在 User App 帳戶頁的用戶名稱下會看到「一般用戶」
2. 用戶成功取消 0 元方案時，系統立刻移除該用戶的 WeMo PASS 會員身份，用戶在 WeMo PASS 頁的用戶名稱下會看到「立即加入 WeMo PASS 享折扣」
3. 用戶成功取消 0 元方案時，系統立刻取消所有 WeMo PASS 會員身份才享有的權益，包含但不限於下方權益：
   1. **取消「3 分鐘騎乘準備時間」權益**
   2. **取消「騎乘趟次折扣」權益**
   3. **取消當下剩餘未使用的「月月 3 趟免費騎（騎乘券）」權益**
      1. 若用戶訂閱後，未使用「月月 3 趟免費騎（騎乘券）」即取消方案，請回收剩餘未使用的 3 張騎乘券
      2. 若用戶訂閱後，使用 1 張「月月 3 趟免費騎（騎乘券）」即取消方案，請回收剩餘未使用的 2 張騎乘券
      3. 若用戶訂閱後，使用 2 張「月月 3 趟免費騎（騎乘券）」即取消方案，請回收剩餘未使用的 1 張騎乘券
      4. 若用戶訂閱後，使用 3 張「月月 3 趟免費騎（騎乘券）」即取消方案，則無動作
4. **在 Apollo 上架 WeMo PASS 方案時，我能標記該方案是否為「0 元體驗方案」**

## CS

作為一個 CS 團隊成員，我希望...

1. 用戶能在 User App 上確認自己是否曾訂閱過 0 元體驗方案
2. 能在 Apollo 查詢用戶是否曾訂閱過 0 元方案（目前已有此功能）
	1. 參考 [https://apollo-qat.wemoscooter.com/user/profile/201437?tab=payment](https://apollo-qat.wemoscooter.com/user/profile/201437?tab=payment)    ![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003104933748.png]]

# Feature Requirement

## Backend

- 用戶成功取消 0 元方案時，系統立刻移除該用戶的 WeMo PASS 會員身份，用戶在 User App 帳戶頁的用戶名稱下會看到「一般用戶」
- 用戶成功取消 0 元方案時，系統立刻移除該用戶的 WeMo PASS 會員身份，用戶在 WeMo PASS 頁的用戶名稱下會看到「立即加入 WeMo PASS 享折扣」
- 用戶成功取消 0 元方案時，系統立刻取消所有 WeMo PASS 會員身份才享有的權益，包含但不限於下方權益：
  1.  取消「3 分鐘騎乘準備時間」權益
  2.  取消「騎乘趟次折扣」權益
  3. 取消當下剩餘未使用的「月月 3 趟免費騎（騎乘券）」權益
      1. 若用戶訂閱後，未使用「月月 3 趟免費騎（騎乘券）」即取消方案，請回收剩餘未使用的 3 張騎乘券
      2. 若用戶訂閱後，使用 1 張「月月 3 趟免費騎（騎乘券）」即取消方案，請回收剩餘未使用的 2 張騎乘券
      3. 若用戶訂閱後，使用 2 張「月月 3 趟免費騎（騎乘券）」即取消方案，請回收剩餘未使用的 1 張騎乘券
      4. 若用戶訂閱後，使用 3 張「月月 3 趟免費騎（騎乘券）」即取消方案，則無動作



- [ ] check how current CS cancel order immdeitely?
	- [ ] do they cancel also take back the rebate voucher?

![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003104948764.png|648]]

endpoint: '/:id(\\d+)/vip/cancel'
endpoint: '/:userId(\\d+)/vip/details/:userVipDetailId(\\d+)



userVipDetail.price怎麼來的?
由 vipPlanPricing 加上 discount mode 的判斷 (percent or amount or null)
const purchasePrice = _calculatePurchasePrice(vipPlanPricing.price, userVipDiscount)


## App - User App

- 用戶在取消 0 元方案時，會看到 [[取消續訂再確認](https://www.figma.com/design/w7lOyqJcGXZhC1xpPUxiiq/WeMo-App?node-id=16429-14458&t=zEPMjnwov559AGOC-0)] 彈窗，若按下「確認」按鈕，畫面回到 WeMo PASS 頁，並顯示 [[取消完成](https://www.figma.com/design/w7lOyqJcGXZhC1xpPUxiiq/WeMo-App?node-id=22107-802&t=zEPMjnwov559AGOC-0)] Toast
- 用戶在取消 0 元方案時，會看到 [[取消續訂再確認](https://www.figma.com/design/w7lOyqJcGXZhC1xpPUxiiq/WeMo-App?node-id=16429-14458&t=zEPMjnwov559AGOC-0)] 彈窗，若按下「我再想想」按鈕，關閉彈窗，畫面停在訂閱管理頁
- 當 [[取消完成](https://www.figma.com/design/w7lOyqJcGXZhC1xpPUxiiq/WeMo-App?node-id=22107-802&t=zEPMjnwov559AGOC-0)] Toast 顯示時，顯示 4 秒會自動消失（參考 [M3 Snackbar Guidelines](https://m3.material.io/components/snackbar/guidelines#4847ca62-bc91-4259-8402-c2247e39361e))

## Web - Apollo

- [Apollo > 價格管理 > WeMo PASS](https://apollo-qat.wemoscooter.com/pricing/wemoPass)
- 進入上面的畫面呼叫  
GET https://apollo-qat.wemoscooter.com/hermes/v20/vip/plans?filter%5B0%5D=planType%7C%7C%24eq%7C%7CNormal&limit=50&offset=0&locale=zh-TW
```ts title:res

{
  "result": "success",
  "data": [
    {
      "id": "47",
      "title": {
        "en": "test-1130",
        "zh-tw": "test-1130"
      },
      "durationMonth": 11,
      "planType": "Normal",
      "continuedPlanId": "47",
      "vipPlanPricing": [
        {
          "id": "72",
          "vipPlanId": "47",
          "beginAt": "2023-11-30T06:56:31.287Z",
          "price": 123,
          "originalPrice": null,
          "description": {
            "en": "",
            "zh-tw": ""
          }
        }
      ]
    },
    {
      "id": "46",
      "title": {
        "en": "買月送月",
        "zh-tw": "買月送月"
      },
      "durationMonth": 1,
      "planType": "Normal",
      "continuedPlanId": "46",
      "vipPlanPricing": [
        {
          "id": "70",
          "vipPlanId": "46",
          "beginAt": "2023-11-28T07:27:30.786Z",
          "price": 49,
          "originalPrice": 99,
          "description": {
            "en": "買月送月測試",
            "zh-tw": "買月送月測試"
          }
        },
        {
          "id": "71",
          "vipPlanId": "46",
          "beginAt": "2023-11-29T16:00:00.310Z",
          "price": 99,
          "originalPrice": null,
          "description": {
            "en": "恢復原價，但名稱無法更改",
            "zh-tw": "恢復原價，但名稱無法更改"
          }
        }
      ]
    }
  ],
  "meta": {
    "total": 20,
    "limit": 50,
    "offset": 0
  }
}


```


新增方案
POST https://apollo-qat.wemoscooter.com/hermes/v20/vip/plans
{
  "planType": "Special",
  "title": {
    "en": "planName0",
    "zh-tw": "planName0"
  },
  "durationMonth": 1,
  "originalPrice": 42,
  "price": 33,
  "description": {
    "en": "desc",
    "zh-tw": ""
  },
  "continuedPlanId": "12"
}

db will add a record in vip plan
![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003105000990.png]]

also a record in vip plan pricing
![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003105001060.png]]


- 新增方案時，若方案類型為「隱藏方案」，增加設定「是否為 0 元體驗方案」選項（等 UI Mockups 規格連結中）。

Nice to have

- [Apollo > 行銷 > WeMo PASS 兌換碼](https://apollo-qat.wemoscooter.com/marketing/promotionCode/wemoPass?tab=list)
- 新增模板時，將「兌換卷標題」欄位，修正為「兌換券標題」
- 新增模板時，將「Description」欄位，修正為「兌換券描述」

# Test Case

# IMC Operation Guideline

### 建立 WPASS 隱藏方案的方法

以下以 Apollo QAT 畫面為例

1. 請至 [Apollo > 價格管理 > WeMo PASS](https://apollo-qat.wemoscooter.com/pricing/wemoPass) 頁面
2. 點「隱藏方案」tab

![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003105017909.png]]

3. 點右上角「新增方案」

![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003105018043.png]]

4. 輸入英文和中文方案名稱 - 必填

![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003105043817.png]]

5. 輸入方案週期（以月為單位）- 必填

![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003105106180.png]]

6. 輸入原始價格（原價）
7. 輸入售價（販售價格）- 必填
8. 輸入英文和中文描述
9. 設定方案到期後是否自動續訂 - 必填

![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003105126649.png]]

10. 承上題，若設定「開啟」，請選擇續訂方案 - 必填

![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003105126748.png]]

11. 點「儲存」送出成功後，即完成建立隱藏方案

### 建立 WPASS 隱藏方案兌換碼的方法

以下以 Apollo QAT 畫面為例

1. 請至 [Apollo > 行銷 > WeMo PASS 兌換碼](https://apollo-qat.wemoscooter.com/marketing/promotionCode/wemoPass?tab=list) 頁面
2. 點「新增模板」

![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003105136020.png]]

3. 輸入 WeMo PASS 兌換碼（用戶要輸入的 code）- 必填

![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003105136119.png]]

4. 輸入英文和英文兌換券標題 - 必填
5. 輸入 Description - 必填
6. 點「新增 Group」 - 必填

![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003105144317.png]]

7. 點「> Group 1 (新增)」展開設定版面

![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003105151538.png]]

8. 設定「指定方案購買」（兌換後可購買的隱藏方案） - 必填

![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003105151702.png]]

9. 設定「兌換次數上限」

![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003105157490.png]]

10. 設定「序號位數」
11. 設定「序號數量」
12. 設定 Exchange Period Begin 和 End - 必填

![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003105202495.png]]

13. 設定 Use Begin At 和 Use End At
14. 點「儲存」送出成功後，即完成建立 WPASS 隱藏方案兌換碼

![[IMG-WPASS 取消 0 元方案，回收剩餘權益-20241003105202608.png]]

\*\*

# **Project Brief** 📖

`說明：用來說明此專案、任務的背景說明和需求，通常會在 PM 發起的 Project Brief 會議中向團隊分享。`

## **1. Request Department**

1. IMC Miya

## **2. Background**

此專案為「**WeMo PASS 用戶留存計畫**」的 **PHASE 1 - 取消 0 元方案，回收剩餘權益**，將針對訂閱「0 元體驗方案（以下簡稱 0 元方案）」調整取消訂閱流程文案 (App) 和相關機制 (Backend)，一但取消成功，未使用權益將立即終止。相關數據：(不分群用戶) 目前 0 元方案的留存率約 20%

此專案參考 Uber One 新會員「免費試用」服務的做法，在免費試用期間，若取消 Uber One，用戶優惠都會在成功取消後立即終止。參考

1. Uber One [https://www.uber.com/tw/zh-tw/u/uber-one/](https://www.uber.com/tw/zh-tw/u/uber-one/)
2. Uber One T&C（見 [4. 取消您的 Uber One] ）[https://www.uber.com/legal/zh-tw/document/?name=renewable-passes-terms&country=taiwan&lang=zh-tw](https://www.uber.com/legal/zh-tw/document/?name=renewable-passes-terms&country=taiwan&lang=zh-tw)

## **3. Goal**

1. 提高 WPASS 0 元方案留存率
2. 降低 WPASS 0 元方案訂閱當天的取消率

## **4. Scope**

1. **WeMo PASS 用戶留存計畫 PHASE 1：取消 0 元方案，回收剩餘權益**
2. 對象：訂閱 0 元體驗方案的用戶
3. 平台：User App、Apollo
4. Apollo：IMC 上架方案時能標記該方案為 0 元方案
5. User App：用戶是否能知道自己曾訂閱過 0 元方案（`確定不排入本次實作`）
6. **WeMo PASS 用戶留存計畫 PHASE 2：取消一般方案的流程優化**（尚未規劃）
7. 對象：訂閱非 0 元體驗方案的用戶

## **5. User Story**

1. 參考 [專案文件 > User Story](https://docs.google.com/document/d/1QZhsefyi4hjGkTFc_Jc1N7wITqMxOzFQ0UM9TerRbTo/edit#heading=h.dm4h9os553h8) 標題

# **Feature ⚙️**

[🔥 [Feature][WPASS] 取消 0 元方案，回收剩餘權益](https://app.asana.com/0/1205309502170758/1206532233236380/f)

## **1. Design Spec**

1. FLOW: [取消 0 元方案的流程](https://www.figma.com/board/p2YTRUJ9qDb9brbrPgzoGY/%E5%85%8C%E6%8F%9B%E5%BA%8F%E8%99%9F?node-id=1709-10168&t=n6GEHZZfEH9jU7LJ-0)
2. User App - UI MOCKUPS: [取消 0 元方案的 UI 畫面](https://www.figma.com/design/w7lOyqJcGXZhC1xpPUxiiq/WeMo-App?node-id=16414-37772&t=CnZ5JnT4QjrFANV3-0)
3. Apollo - UI MOCKUPS: [設定體驗方案的 Apollo UI 畫面](https://www.figma.com/design/8GORW3KqwUKkyB1QrXCMf7/Apollo?node-id=9657-35&t=K6kMtqmEOYGctRuY-0)

## **2. Feature Requirement**

1. 參考 [專案文件 > Feature Requirement](https://docs.google.com/document/d/1QZhsefyi4hjGkTFc_Jc1N7wITqMxOzFQ0UM9TerRbTo/edit#heading=h.vcwanb8wxbj) 標題
