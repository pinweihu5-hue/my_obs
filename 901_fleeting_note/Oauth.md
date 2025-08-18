# 完整解說
### what is Oauth?
- OAuth 是一種標準協議，允許使用者與您網站的伺服器安全地共享資訊，例如他可以分享他在 Google 帳戶的電子郵件地址，但不需要提供 Google 登入憑證(credentials)。


### 流程
- 使用者選擇使用 Google 登入：使用者造訪您的網站並點擊「使用 Google 登入」按鈕
- 您的網站要求 Google 許可：您的網站將使用者重新導向至 Google。過程中，你需要向 Google 提供 client id 來識你的網站
- Google 尋求使用者同意：Google 會顯示同意畫面，詢問使用者是否同意與您的網站分享其 Google 詳細資訊（例如電子郵件和姓名）。
- 使用者同意並接收程式碼：在使用者同意後，Google 會將使用者重新導向回您的網站，並在 URL 的查詢參數中包含授權代碼(auth code)。
- 與 Google 交換代碼：您網站的伺服器將此授權代碼(auth code)和用戶端金鑰(client secret)傳送給 Google，以驗證您網站伺服器的身份。然後 Google 發回一個訪問令牌(access token)。
- 您網站的伺服器取得使用者資訊：使用此存取權令牌(access token)，您網站的伺服器現在可以向 Google 詢問使用者資訊，例如電子郵件和姓名。然後，您可以使用此資料對使用者進行身份驗證以使用您的服務。



### step1: 準備工作: setup up OAuth Credentials (Google, FB...etc) 
- 首先，您需要 Google 向您頒發 CLIENT_ID 和 CLIENT_SECRET 。
	- 這些令牌確認請求源自您的伺服器。
	- 請造訪 [Google 的身份驗證設定指南](https://developers.google.com/identity/protocols/oauth2/web-server#creatingcred)，以了解取得這些令牌的詳細步驟
- 確保 CLIENT_SECRET 的安全並且只能在伺服器程式碼中存取非常重要。
	- 最佳實踐是使用環境變數來管理憑證，don't hard coded。


### step2: 處理 Oatth client 端
- 當使用者造訪您的網站時，他們會看到使用 Google 登入的選項。
- 此功能是透過簡單的 HTML 錨點 `<a>` 元素實現的，將使用者的瀏覽器定向到 /oauth/google 

```
<html>
  <head>
    <title>oauth walkthrough</title>
  </head>
  <body>
    <a href="/oauth/google">login with google</a>
  </body>
</html>
```


### step3: 重定向至 Google 以徵得用戶同意
- 當網頁瀏覽器請求您的 /oauth/google 端點時，您的伺服器會透過將使用者重新導向至 Google 來回應。
- **此重定向需要將特定查詢參數 ( ?key=value ) 附加到 URL**。下面對各參數進行說明。
```ts

app.get("/oauth/google", (req, res) => {
  const params = new URLSearchParams();

  // 用哪一種 Authorization Code grant type, 這邊用 code, 這是 server based 的常見作法
  // 也支援 SPA 應用和 machine to machine 的其他 type
  params.set("response_type", "code");

  // 告訴 OAuth 提供者哪個應用程式, clinet_id, 正在發起請求
  params.set("client_id", CLIENT_ID);


  // 此參數指定在使用者登入 Google 帳戶並同意與您的應用程式分享其資訊後，Google 應將使用者重新導向到何處
  // 您也必須在 Google 開發者控制台（您在其中取得 CLIENT_ID 和 CLIENT_SECRET ）註冊您的重定向 URI。
  params.set("redirect_uri", "http://localhost:3000/oauth/google/callback");

  // 指定您的伺服器打算存取的有關使用者的數據，在本例中是他們的電子郵件。
  // 當使用者被重新導向到 Google 時，系統會要求他們同意與您的服務分享此資訊。
  // 谷歌提供了一系列存取各種服務的範圍，包括閱讀用戶的電子郵件。
  // 不過，請求存取敏感資料（例如使用者的電子郵件）可能需要您的服務接受 Google 的審核。
  params.set("scope", "email");


  // state 是一個可選查詢參數，可以保存任何字串值，但通常會設定為隨機產生的字串以進行安全檢查。
  // 當使用者被重新導向回您的 redirect_uri 時，此參數將傳回與最初傳送給 Google 的相同值。
  // 然後，您的伺服器檢查該字串是否與其最初發送的字串相符。如果它們不匹配，OAuth 進程將停止。
  // 此步驟可以確保是您的網站（而不是攻擊者的網站）發起了 OAuth 流程，防止跨網站請求偽造 (CSRF) 攻擊。 
  const state = crypto.randomBytes(16).toString("hex");
  params.set("state", state);



  // 接下來，建立用於將使用者重新導向至 Google 驗證頁面的 URL。
  // 透過設定 Set-Cookie 標頭，您可以將 state 變數儲存為 cookie 在使用者的瀏覽器中。
  // 當 Google 將使用者重新導向回您的網站時，您將驗證 cookie 中儲存的 state 是否與 Google 在查詢參數中包含的 state 相符。
  const url = `https://accounts.google.com/o/oauth2/v2/auth?${params.toString()}`;
  res.set("Set-Cookie", `oauth_state=${state}; HttpOnly; Secure; SameSite=Lax`);
  res.redirect(url);
});


```


### step4: 谷歌徵求用戶同意
- 用戶現在位於 Google 的同意畫面，他們在其中選擇自己的 Google 帳戶並同意共享先前由 scope 參數指定的資料。
- **確認後，Google 會指示瀏覽器重新導向到您指定的 redirect_uri ，並將 code 和 state 作為查詢參數附加到 URL。**


### step5: 與 Google 交換 codes
- 瀏覽器重定向到 http://localhost:3000/oauth/google/callback 並呼叫您的 /oauth/google/callback 端點。
- 在這裡，您從查詢參數中提取程式碼和狀態。然後，您檢查 Google 返回的狀態是否與您最初產生的狀態相匹配，從而確保請求的完整性。
- 驗證完成後，使用 `exchangeCodeForToken` 函數將 Google 提供的程式碼交換為存取權杖。

```ts

async function exchangeCodeForToken(code: string) {


  const resp = await fetch(`https://oauth2.googleapis.com/token`, {
    method: "POST",
    headers: {
      "Content-Type": "application/x-www-form-urlencoded", // <-- 要用這個 Content-Type
    },
    body: new URLSearchParams({
      grant_type: "authorization_code",  // 告訴 Google 您打算用先前收到的 `code` 來交換 access token
      code: code,  // The code you received from Google in the request

      // 與向 Google 發出的初始請求一樣，您再次包含 redirect_uri 參數。 
      // Google 會驗證它是否與發出程式碼時使用的原始 redirect_uri 相匹配，作為額外的安全性檢查
      // 此請求不會嘗試將使用者重定向到 redirect_uri ；此參數僅用於驗證目的
      redirect_uri: "http://localhost:3000/oauth/google/callback",

      // 用 client_id 向 Google 識別自己，並透過包含 client_secret 來確認其身分。
      // 這讓 Google 知道該請求直接來自您的伺服器，而不是冒名頂替者的
      // 您可能想知道為什麼 client_secret 不是初始使用者重新導向到 Google 同意畫面的一部分。
      // 因為將 client_secret 包含在 URL 中會將我們的 client_secret 暴露給用戶，從而面臨被攻擊者利用的風險。
      // 攻擊者可能會模仿我們的網站，欺騙用戶分享敏感資訊。
      // 透過將 client_secret 只在伺服器到伺服器才進行傳遞，可以保護 client_secret 不被外洩。
      client_id: CLIENT_ID,
      client_secret: CLIENT_SECRET,
    }).toString(),
  });

  if (!resp.ok) {
    throw new Error("Something went wrong");
  }

  // 您會收到包含 access_token 鍵的 JSON 回應。
  // 透過此令牌，您可以向 Google 要求使用者訊息，例如他們的電子郵件地址。
  return resp.json() as Promise<{ access_token: string }>;
}



```



### step6: Your Website's Server Gets the User's Info
1. 使用 getUserInfo helper  函數，傳入新取得的 accessToken 。
2. 在 getUserInfo 內，向 Google 的 userinfo 端點發送請求，並在 Authorization HTTP 標頭中附加帶有 Bearer 前綴的 accessToken 。
3. 成功的回應會以 JSON 形式與使用者的電子郵件一起傳回。

對於演示，伺服器只需傳回從 Google 收到的 userInfo 。在實際應用程式中，此電子郵件可用於對使用者進行身份驗證，為您的服務建立會話。
```ts

app.get("/oauth/google/callback", async (req, res) => {
  const { code, state } = req.query;
  const oauthState = getCookie("oauth_state", req.headers.cookie as string);

  // 上面提到的，防止 CSRF
  if (state !== oauthState) {
    return res.status(400).send("Invalid state");
  }

  const accessToken = await exchangeCodeForToken(code as string);

  // 1. 傳入新取得的 accessToken
  const userInfo = await getUserInfo(accessToken);

  res.header("Content-Type", "application/json").send(JSON.stringify(userInfo));
});








async function getUserInfo(accessToken: string) {
  const resp = await fetch(`https://www.googleapis.com/oauth2/v2/userinfo`, {
    headers: {
      Authorization: `Bearer ${accessToken}`,
    },
  });

  return resp.json() as Promise<{ email: string }>;
}

```



## step 7: 了解專有名詞

- 為簡單起見，本指南使用「使用者」、「伺服器」和「Google」等非正式術語。但是，在 OAuth 文件中，您將遇到這些實體的標準化名稱
- **資源擁有者** (Resource Owner)：您正在存取其資料的實體，稱為「使用者」。這就是您嘗試取得的電子郵件地址的擁有者。
- **使用者代理**(User Agent)：在指南中稱為「瀏覽器」。它是資源擁有者與您的應用程式互動的介面。其他範例包括行動應用程式和 CLI 終端。
- **OAuth Provider** ：在範例中，此角色由 Google 擔任。 OAuth 提供者促進 OAuth 驗證流程、管理您的 `CLIENT_ID` 和 `CLIENT_SECRET` 並託管同意畫面。 Facebook、微軟和 GitHub 是其他例子。
- **客戶端** (client)：該術語描述了 Web 伺服器。它是尋求存取資源所有者資料的實體。
- **授權伺服器** (Authorization Server)：Google 的一個元件，用於發布授權程式碼並允許您用該 `code` 交換 `access_token` 。
- **資源伺服器**(Resource Server)：Google 的一部分，如果您擁有有效的 `access_token` 則傳回使用者資訊（例如電子郵件）。




## Step8: 後續
- 您已使用授權代碼授予類型建置了 OAuth 用戶端。但是，您應該從頭開始開發自己的 OAuth 用戶端嗎？一般來說，不會。微妙的錯誤或新穎的漏洞可能會帶來嚴重的安全風險。相反，選擇經過充分測試的開源 OAuth 用戶端是更安全的選擇。
- 了解 OAuth 客戶端的內部工作原理非常重要。它讓您了解開源程式庫中可用的設定選項，幫助解決 OAuth 相關問題，並使學習其他 OAuth 流程（例如刷新、PKCE 和隱式 / refresh, PKCE, and implicit a breeze）變得輕而易舉。您也可以很好地了解 OpenID Connect (OIDC)；建構在 OAuth 之上的協定。
- 



ref:
[透過建立您自己的 OAuth 客戶端來學習 OAuth 2.0 --- Learn OAuth 2.0 by Building Your Own OAuth Client](https://annotate.dev/p/hello-world/learn-oauth-2-0-by-building-your-own-oauth-client-U2HaZNtvQojn4F-U2HaZNtvQojn4F)



## 個人筆記小結

簡單流程回顧
- 先去 Oauth provider 建立帳號，設定秘密和 callback
	- like go to google 設定 client id and client secret and callback endpoint
- 使用者點"用 google 登入", 這邊 we need to rediect user to google 登入畫面和使用者授權資料的畫面 via a GET request to Google 提供的 oauth endpoint
	- 這個 GET req 需要傳入需要必要的資訊 via query param:
		- callback, auth type, csrf token, scope, client screct...etc
- 使用者同意授權給我們他的 Google 資訊後, Google 會呼叫(http GET request) 一個我們定義好的 callback endpoint, 我們可以從 endpoint param 中拿到 auth_code (webhook)
- 我們 POST 呼叫一個 google 提供的 endpoint , use auth_code 換 token
- 這個 token 就可以拿到使用者的 email


哪裡設定我要拿使用者的那個資料?
- rediect 到 google 登入畫面的 這個這個 GET redirect 的 endpoint 的 param 中需要設定
	- `params.set("scope", "email");`


哪一個步驟可以防止 請求偽造 CSRF 攻擊?
- 一開始 我們 redirect 到 google 登入畫面的 param 要設定 一組 randomBytes as CSRF token
	- `params.set("state", crypto.randomBytes(16).toString("hex"));`
- 然後 google 呼叫我們設定的 callback 會帶上這個 token 放在 req.headers.cookie。
- 我們檢查是否一樣。
- 這可以確保是我們自己的網站發起流程，而不是其他攻擊者的網站。


前端有哪些工作?
- 需要呼叫BE新寫的 API

後端有哪些工作, two new endpoints, two request
- GET, 點了"用 google 登入", 重定向的那個 redirect endpoint, shall be a BE endpoint, since we have CSRF 機制, we will check if CSRF token is the same
- GET, Google 會呼叫的 callback endpoint
- 呼叫 Google endpoint 來由 code 換 access token
- 呼叫 Google endpoint 來由 access token 拿到 使用者的 email


---



# Oath2 note from 成為看起來很強的後端

![[IMG-Oauth-20250810200239047.png]]

比較舊的會傳 code, 那就還要去跟第三方拿 token
比較新的會直接給 token
(上面 google 的例子的  GET 的 response 才會拿到 code, 還需要打另一個 API 去要 token)

透過 type 可以選，不過還是看第三方支援
![[IMG-Oauth-20250810200240002.png]]


要跟第三方獲取哪些授權, 透過 scope 參數
![[IMG-Oauth-20250810200241189.png]]


這邊也有兩種作法，你要導到前端還是後端?
下面的例子，就是導到後端server, api.example.com, 然後後端去拿資料，然後發給前端 301 redirect 到 登入後的網站
![[IMG-Oauth-20250810200242527.png]]
做在後端比較安全
因為你就可以設定白名單，只有 fb 可以打進來我們這個後端, 最後也是後端告訴前端要去哪裡
token 也都在內網
另外 github, google, 這邊傳 token 都有符合 openID的規範，就是會是 jwt
資料就是解 jwt 就可以拿到了



# 其他




notion  link
[(309) 403 01 如何有效地建構技術方案？ By 林世鵬 - YouTube](https://www.youtube.com/watch?v=y6EFcpiEd9U&list=PLQdC95bqNnA9cSJIMgVmmoq89cfRj2kkP)
-  [Notion – The all-in-one workspace for your notes, tasks, wikis, and databases.](https://www.notion.so/nture4388/309-403-01-By-YouTube-3d59e61903b64c8c8db9b3038c6e5126?pvs=4)

- more on auth
	- [OAuth](https://thecopenhagenbook.com/oauth)



### SSO

認證機制
假設我想要使用 A 的服務時 必須先透過 B 進行登入認證才能使用

SSO 例子：

我先在Google月曆登入，使用後。
接著我跳去Gmail, 我發現我不用登入可以直接用。
（因為背後Google透過cookie-session等機制讓我不需要重新登入）


---


[line notify](https://www.notion.so/line-notify-87463cebe1b84287b53a4f2389f4995e?pvs=21)

