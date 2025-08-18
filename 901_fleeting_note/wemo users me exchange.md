[[wemo_code_note]]

---

# contoller level
await SentryTokenService.exchange(wemoSentry)


res schema
export const ExchangeResponseSchema = z.object({
  accessToken: z.string().describe('WeMo User Access Token'),
  refreshToken: z.string().describe('WeMo User Refresh Token')
})

# svc level
進入 exchange 有四種可能
```ts

    switch (sentryInfo.authFlow) {
      case AuthFlow.register:
        user = await exchangeUserForRegister(sentryInfo)
        break
      case AuthFlow.login:
        user = await exchangeUserForLogin(sentryInfo)
        break
      case AuthFlow.appReviewer:
        user = await exchangeUserForAppReviewer(sentryInfo)
        break
      case AuthFlow.changeDevice:
        user = await exchangeUserForChangeDevice(sentryInfo)
        break
      default:
        throw new Error(CustomErrorV2.SENTRY_TOKEN_UNKNOWN_AUTH_FLOW.type)
    }

```

以 exchangeUserForLogin 為例
- isVerified 確認 不同狀態(mail | phone | superCode)有帶上應該帶的資料
- 建立 userAuth and userLoginHistory
- 返回 user


如果是 exchangeUserForRegister
- isVerified 確認 不同狀態(mail | phone | superCode)有帶上應該帶的資料
- 確認 idcard 是否有 duplicate
- init 使用者資料 - db.user.create(userItemsVO  
- init vehicle歷史資料 - vehicleStatusModifyHistory
- 建立 userAuth and userLoginHistory



# 上面處理完了，會..
- postUpdateProcess 驗證成功後更新必要資訊到 User 表上
- checkPermissionAndGenerateToken(user)
	- 確認 isAllowLogin is true (from UserPermission table)
	- update ttl 20 秒後刪除 sentryToken
	- tokenManager.genUserTokens -> 最後 get access and refresh token
		- 這段 gen jwt token
		    - module.exports.genUserAccessToken = (userId, opt = {}) => {
		    - 這邊用 jwt.sign 來 gen
		- 也 gen refresh token
		  - module.exports.genUserRefreshToken = (userId) => {
			  - crypto.createHmac('sha256', SECRET).update(`${userId}.${moment().utc().unix()}`).digest('hex').toUpperCase()
			  - 基本上就是 sha256 hash and salt (use timesstamp as salt)
		  - access token save into redis, ttl in 5 says
		  - refresh token update into db user table



題外話
登出會呼叫
清掉 redis access token and update refresh token to empty string
```ts

  async deleteTokenByUserId(userId: string) {
    await this.store.del('WeMo', userId)
    await db.user.updateRefreshToken(userId, '')
  }

```