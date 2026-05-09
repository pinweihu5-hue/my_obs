# 10 Principles for Agent-Native CLIs

## 背景

作者上個月寫了 7 條 Agent-Friendly CLI 原則，這次擴展成 10 條，靈感來自 Cloudflare 和 HeyGen 的 CLI 設計實踐。核心觀點：**為 Agent 設計 CLI 優先，人類也會受益**，而非反過來。

---

## Tier 1：基本門檻（不搞砸 Agent）

這 5 條是防禦性的，做不到的話 Agent 每次呼叫都要付出代價。

### 1. 預設非互動模式
指令必須能在沒有互動提示的情況下執行。當 subagent 在背景 spawn 一個 process，沒有人能回答 `Are you sure? [y/N]`，指令就會永遠掛在那。

- 每個可能提示的指令都要有 `--force` / `--yes` / `--no-input`
- 誠實地偵測 TTY，非 TTY 就當作 headless 模式
- Cloudflare 的規則：一律用 `--force`，禁止 `--skip-confirmations`

### 2. 結構化、可解析的輸出
Agent 要的是 JSON，不是漂亮的 ANSI 彩色表格。

- 每個回傳資料的指令都要有 `--json`
- stdout = 資料，stderr = 診斷訊息，exit code = 錯誤分類
- **關鍵：統一用同一個 flag 名稱**，不要有的指令用 `--json`、有的用 `--format=json`

### 3. 錯誤訊息要教 Agent 怎麼修正
這是最精華的一條。當 Agent 傳了無效值，錯誤訊息不只說「錯了」，還要列舉有效值。

```bash
# 爛的錯誤訊息
error: invalid visibility

# 好的錯誤訊息 — Agent 一次 retry 就能自我修正
error: --visibility must be one of: public, private, unlisted (got: "secret")
```

### 4. 安全的 retry 與明確的變異邊界
Agent 會一直 retry，所以 create 必須是幂等的（idempotent），第二次呼叫回傳已存在的資源而非重複建立。破壞性操作需要 `--dry-run` 預覽，且必須用顯式的 flag（如 `--force`）才能執行。

### 5. 每一層都要限制回應大小
預設分頁（例如 20 筆），回傳截斷提示教 Agent 如何縮小查詢範圍。另一個層面：**MCP tool description 也要控制 token 數**，Cloudflare 把 3000+ 個 API 操作的 MCP description 控制在 1000 tokens 以內。

---

## Tier 2：複利效應（越用越好用）

這 5 條是進攻性的，讓 CLI 越被 Agent 使用就越有價值。

### 6. 跨 CLI 詞彙一致性
Agent 不是只學你一個 CLI，它是從所有 CLI 的經驗中建立通用模型。當你的指令用 `info` 而全世界都用 `get`，Agent 不會失敗，但會**慢**——要燒更多 token 去看 `--help`。

Cloudflare 的規則（用 schema 強制執行，不是靠 code review）：
- 永遠用 `get`，不用 `info`
- 永遠用 `list`，不用 `ls`
- 永遠用 `--force`，不用 `--skip-confirmations`
- 永遠用 `--json`，不用 `--format=json`

> "靠人工 review 來強制一致性是瑞士起司——到處是洞"

### 7. 三層內省（Introspection）
不只 `--help`，而是三層結構：

| 層級 | 用途 | 格式 |
|------|------|------|
| `--help` | 這個指令做什麼？ | 人類可讀文字 |
| `agent-context` | 所有指令的完整結構長怎樣？ | 結構化、有版本號的 JSON |
| `skill-path` / SKILL.md | 什麼時候該用這個指令？ | 長篇教學 prose |

`agent-context` 要有 `schema_version` 欄位，讓 Agent 能偵測 breaking change。

### 8. Async 感知的執行
大部分 CLI 對 async API 的處理是：submit 回傳 job ID，然後就撒手不管了。Agent 要自己寫 polling loop——浪費 token 又容易寫錯。

解法是 `--wait`：

```bash
# 沒有 --wait：Agent 要自己 polling
$ mycli video render --script=story.txt
{"job_id":"job_8f2a","status":"queued"}
# Agent 還要不斷查狀態...

# 有 --wait：一條指令搞定
$ mycli video render --script=story.txt --wait
{"job_id":"job_8f2a","status":"complete","url":"https://.../out.mp4"}
```

背後的 job ledger（存在 `~/.<cli>/jobs.jsonl`）讓 Agent 中斷後重連時能找到正在跑的 job，不會重複提交。

### 9. 透過 profile 實現持久身份
Agent 不是只來一次，它明天、下週還會再來。不該每次都要重新指定相同的 8 個 flag。

```bash
# 儲存一次設定
$ mycli profile save my-podcast --avatar=lila --voice=warm-en

# 之後每次只用一個 flag
$ mycli video create --profile=my-podcast --script=ep_42.txt
```

優先級：顯式 flag > 環境變數 > profile > 預設值。Profile 名稱要出現在 `agent-context` 中，讓 Agent 能發現有哪些可用身份。

### 10. 雙向 I/O
兩個新機制：

**輸出端 — `--deliver`**：把產出直接送到目的地，不用多跳一步。
```bash
--deliver=stdout           # 輸出到 stdout
--deliver=file:./out.mp4   # 寫入檔案
--deliver=webhook:https://...  # POST 到 webhook
```

**輸入端 — `feedback`**：Agent 遇到摩擦時（flag 被拒、race condition、錯誤訊息不列舉），有個頻道可以回報給維護者。
```bash
$ mycli feedback "the --tier flag rejects 'enterprise' but the docs list it as valid"
```

預設存在本地，設定環境變數後可以 POST 到上游。否則維護者永遠不會知道 Agent 用得很痛苦。

---

## 底層架構的關鍵

Tier 2 的大部分原則**靠人工寫很難一致，靠 schema/codegen 卻很簡單**。Cloudflare 的核心洞察：用一份 TypeScript schema 同時產生 CLI、SDK、Terraform provider、MCP server，這樣所有原則在所有操作上都不會漂移。

---

## 一句話總結

> 與其先為人類設計再為 Agent 打補丁，不如先為 Agent 設計。好的 Agent CLI 設計，人類自然也會覺得好用。
