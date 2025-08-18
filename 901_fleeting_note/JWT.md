
# JWT 機制是如何做到無狀態驗證的？

# 資訊自包含
JWT 是一個自包含的令牌，這意味著它包含了所有必要的信息來驗證用戶的身份。JWT 通常由三部分組成：
- **標頭（Header）**：包含令牌的類型（通常是 "JWT"）和所使用的簽名算法（如 HMAC SHA256）。
- **有效負載（Payload）**：包含用戶的聲明（claims），例如用戶 ID、角色、過期時間等。
- **簽名（Signature）**：用於驗證令牌的完整性和來源。這是通過將標頭和有效負載進行編碼後，使用密鑰進行簽名生成的。
- 結構可以看下面

服務器可不需要存儲任何會話狀態:
- 在傳統的會話驗證中，服務器需要存儲用戶的會話狀態（例如，用戶 ID 和會話過期時間）
- 這意味著服務器需要維護一個會話存儲（如數據庫或內存）。
- 而在 JWT 機制中，所有的驗證信息都包含在令牌中，服務器不需要存儲任何會話狀態。這使得 JWT 成為無狀態的，因為每次請求都可以獨立處理。

![[IMG-JWT-20250810200237129.png]]



![[IMG-JWT-20250810200238193.png]]
related: [[XSS]] and [[CSRF]]




# jwt.io
- [JSON Web Tokens - jwt.io](https://jwt.io/)
- 你也可以把 token 貼到這邊看一下裡面 "沒有被 hash 的"前兩個部分 (header and payload)
	- 所以這邊只會被 encode, 所以這邊大家都可以看到，所以這邊你不可以放 sensitive data
- 你改上面兩個部分， encoded 前兩個地方會變
- 你改 秘密，第三簽名的地方會 hash 出不同的簽名
- 就是透過這個簽名(only server 自己持有的)， server 可以 verify client 帶上來的 token 第三部分的簽名，是否自己本來hash 後派發出去的。



這是 wemo user app 的 payload
![[IMG-JWT-20250810200239212.png]]

# server 如何驗證簽名?

1. **接收 JWT**： 當客戶端發送請求時，JWT 通常會附加在 HTTP 標頭中，例如：
    
    `Authorization: Bearer <your_jwt_token>`
    
2. **分解 JWT**： JWT 是由三部分組成的，這三部分用點（`.`）分隔。

	服務器需要先將 JWT 分解為三個部分：
    
    - **Header**：包含令牌的類型和簽名算法。
    - **Payload**：包含用戶的聲明（claims）。
    - **Signature**：用於驗證的簽名。
    
    例如，JWT 的格式如下：
    
    `<header>.<payload>.<signature>`
    
3. **解碼 Header 和 Payload**： 服務器使用 Base64Url 解碼算法將 Header 和 Payload 進行解碼，獲取原始的 JSON 對象。這些信息將用於後續的驗證和處理。
    
4. **驗證簽名**： 簽名的驗證是 JWT 的核心部分。根據 JWT 的簽名算法（在 Header 中指定），服務器將執行以下步驟：
    - **重建簽名**： 服務器使用相同的算法和密鑰（對稱加密）或私鑰（非對稱加密）來重建簽名。具體步驟如下：
        - 將解碼後的 Header 和 Payload 進行串聯，並用點（`.`）分隔。
        - 使用指定的簽名算法和密鑰（或私鑰）對這個串聯的字符串進行簽名，生成新的簽名。
    - **比較簽名**： 將重建的簽名與 JWT 中的簽名部分進行比較。如果兩者相同，則表示 JWT 是有效的，且未被篡改；如果不同，則表示 JWT 可能已被篡改或無效。



hermes related code
```ts title:isValidManagerToken fold 

const isValidManagerToken = (token: string) => {
  try {
    const subStringArray = token.split('.')
    return (
      crypto.createHmac('sha256', MANAGER_AUTH_SECRET).update(`${subStringArray[0]}.${subStringArray[1]}`).digest('hex').toLowerCase() ===
      subStringArray[2].toLowerCase()
    )
  } catch (error) {
    logger.error({
      tag: 'token-helper/isValidToken',
      message: `Fail to parse token, token: ${token}`,
      stack: error instanceof Error && error.stack
    })
    throw new Error('Fail to parse token')
  }
}

```

```ts title:genUserTokens fold

  async genUserTokens(userId: string, opt?: TokenOpt): Promise<Token> {
    const tag = 'tokenManager/genUserTokens'
    try {
      const accessToken = genUserAccessToken(userId, opt)
      const refreshToken = genUserRefreshToken(userId)
      await this.store.set('WeMo', userId, accessToken, this.liftTime)
      await db.user.updateRefreshToken(userId, refreshToken)
      return { accessToken, refreshToken }
    } catch (error) {
      logger.error({ tag, error })
      throw error
    }
  }

```

```js title:genUserRefreshToken fold

module.exports.genUserRefreshToken = (userId) => {
  try {
    return crypto.createHmac('sha256', SECRET).update(`${userId}.${moment().utc().unix()}`).digest('hex').toUpperCase()
  } catch (error) {
    logger.error({
      tag: 'token-helper/gen Refresh Token',
      msg: `Fail to encode userId, userId: ${userId}`,
      stack: error.stack
    })
    throw new Error('Fail to encode userId')
  }
}

```

        
5. **檢查過期時間**： 在驗證簽名之後，服務器還需要檢查 JWT 的有效性，包括：
    - **過期時間（`exp`）**：如果 JWT 中的過期時間已過，則該令牌無效。
    - **其他聲明**：根據應用需求，可能還需要檢查其他聲明（如 `nbf`、`iat` 等）。
6. **處理請求**： 如果簽名驗證成功且 JWT 仍然有效，服務器可以根據 Payload 中的聲明（如用戶 ID、角色等）來處理請求，並執行相應的業務邏輯。
    





# why use access token + refresh token

使用者登入後，會 issue 2 token

一個 access token (JWT) 和一個 refresh token（可以是任何合理長度的唯一字串）
refresh token 會根據使用者 ID 儲存到資料庫中。

access token (JWT) 內部通常會包含使用者 ID 和 user role（if applicable to your application）。

客戶端會在每個發送到 API 的請求標頭中發送 access token。 你的 API 中間件將驗證 JWT 並將使用者 ID 設定在請求的「上下文」中。 
這允許你在請求的其餘部分以安全的方式知道使用者的 ID，而無需每次都耗費資源到資料庫驗證。

access token 應在 X 分鐘後過期（5 分鐘到數小時，取決於你的應用程式的適當時間），當它過期時（或即將過期之前），你將 refresh token 發送到 API，API 會根據第一步中的資料庫進行驗證。 驗證成功後，系統會向客戶端發布新的 access token 和 refresh token，並將舊的 refresh token 在資料庫中失效 / 刪除。

你只在使用 refresh token 時才訪問資料庫的原因是，它允許你克服 JWT 的弱點（JWT 無法主動撤銷，因此好的方法就是設定比較短的失效時間），因為如果需要，你可以在 X 分鐘內（在上面的示例中為 5 分鐘）有效地將使用者登出，例如發生安全漏洞或使用者只想登出特定 / 所有活動裝置。

這種組合可以提高效能（不必每次請求都訪問資料庫來確定使用者是誰）和 安全性（儲存到資料庫中的 refresh token 可以被刪除 / 撤銷，並且使用者需要每 X 分鐘刷新他們的 access token）。

理想情況下，你的 Node.js 伺服器會在僅限 HTTP 的安全 Cookie 中設定 access/refresh token 回應，但有時並非總是可行（例如，原生應用程式面臨的挑戰）。

編輯：你也需要在將 refresh token 儲存到資料庫之前對其進行雜湊處理。 如果你不這樣做，並且你的資料庫洩漏，那麼在您意識到並撤銷所有 refresh token 之前，任何人都可以以任何使用者的身份登入。


總結：
- Used to obtain new access tokens without having to re-authenticate the user
- Refresh tokens can be compromised and used to continually generate access tokens
- Ways to enhance security of refresh tokens:
	- refresh token rotation (one-time use for RT)
	- create a whitelist of refresh tokens

![[IMG-JWT-20250810200240407.png]]
source: [How Does JWT Authentication Work? (JSON Web Token) | Tokens vs Sessions - YouTube](https://www.youtube.com/watch?v=hoBSjmrwF1k)




ref: [About JWTs : r/node](https://www.reddit.com/r/node/comments/1dwyd8p/about_jwts/)


# JWT Best practice
 
- Keep tokens small (some servers have size limits on the request headers)
- Don't store sensitive data on the token (上面說過 why了)
	- 因此不可以放 pii, like 名字地址等等，放個 user-id, mail之類的還可以
- Have short expirations and implement refresh tokens
- Set the “issuer" (iss) and "audience” (aud) properties in the token payload，好處是你總是可以知道context
- 前端
	- jwt 不要放 localstorge → 因為 JS 程式可以存取，如果遇到腳本攻擊就直接拿走了
	- 不要放sessionStorage → 關掉瀏覽器就要重新登入
	- 放在 cookie
		- 加上secure -> only https can allow bring cookie
		- httpOnly -> 開發者是無法透過程式去拿到 cookie內的資料 , 只有瀏覽器可以拿到
		- sameSite（至少lex) -> 跨源 can not bring cookie
		- 也可以再加上 csrf token 如果有必要的話
example
```json

{
  "iss": "myapp-auth-service", // "JWT 的颁发者", 如果你有一个微服务架构，可能会使用服务名称作为颁发者
  "aud": ["https://api.myapp.com", "https://another-service.com"], // 如果你的 API 服务是 `api.myapp.com` and 'https://another-service.com' ，你可以将 `aud` 這樣設定
  "sub": "user123",  // 主题，表示用户的唯一标识
  "exp": 1672531199,  // 过期时间戳
  "iat": 1672527599   // 签发时间戳
}

```






# link
see notion:  [Notion – The all-in-one workspace for your notes, tasks, wikis, and databases.](https://www.notion.so/nture4388/JWT-1249df69750f806fa16ecd41c8230c51?pvs=4)


- [Day17-Session 管理（一）](https://ithelp.ithome.com.tw/articles/10276212)
- [Day18-Session 管理（二）](https://ithelp.ithome.com.tw/articles/10276747)