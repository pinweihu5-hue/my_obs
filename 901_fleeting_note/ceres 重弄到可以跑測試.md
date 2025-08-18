[[ceres]]
[[wemo_code_note]]


```c fold:error

 FAIL  test/v3/order.test.js
  ● Test suite failed to run

    TypeError: Cannot read property 'constructor' of undefined

      at Object.<anonymous> (node_modules/cli-highlight/src/theme.ts:4:26)
      at Object.<anonymous> (node_modules/cli-highlight/src/index.ts:3:1)
      at Object.<anonymous> (src/platform/PlatformTools.ts:3:1)
      at Object.<anonymous> (src/driver/sqlserver/SqlServerDriver.ts:9:1)
      at Object.<anonymous> (src/migration/MigrationExecutor.ts:7:1)

Test Suites: 1 failed, 1 total
Tests:       0 total
Snapshots:   0 total
Time:        2.241 s
Ran all test suites matching /test\/v3\/order.test.js/i.
npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! ceres@1.32.2 test_one: `cd dist && TZ=UTC NODE_ENV=test DB_SYNC=true jest --collectCoverage=false --config ../jest.config.js "test/v3/order.test.js"`
npm ERR! Exit status 1
npm ERR! 
npm ERR! Failed at the ceres@1.32.2 test_one script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/re4388/.npm/_logs/2024-05-20T04_59_20_797Z-debug.log

```

=> 看來是 ceres node_module髒掉

# 步驟
- git clone
- npm i
- npm run build
- add below command to package.json script
```

"test_ben_all": "cd dist && TZ=UTC NODE_ENV=test DB_SYNC=false jest test/ --runInBand --config ../jest.config.js",

"test_one": "cd dist && TZ=UTC NODE_ENV=test DB_SYNC=false jest --collectCoverage=false --config ../jest.config.js",

"start_ben": "cd dist/ && TZ=UTC PORT=3001 NODE_ENV=development nodemon index.js",

```
- run test -> fail, db 問題
- 加 .nvmrc
	`lts/erbium`
- 改 `config/test.json`
```js hl:6,10,17

"db": {
"client": "postgresql",
"connection": {
"host": "127.0.0.1",
"port": 5461, 
"database": "postgres",
"schema": "public", 
"user": "postgres", // 
"password": "password",
"logging": true

},

"redis": {
"host": "127.0.0.1",
"port": "6389"

},


```
- npm run build again
- 可以跑test 成功了
- 如果要開發，也要跑 `npx tsc --watch`
	- 參考 [[vscode_tsc_watch_auto_run]]

