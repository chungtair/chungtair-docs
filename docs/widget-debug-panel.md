---
title: "Connection Debug Panel — Engineer Survival Kit"
description: "Walkthrough of the in-dashboard Connection Debug panel that surfaces the most recent HTTP exchange per Widget endpoint. Covers the 30-second active connection test, three-way copy buttons (cURL / plain text / full report), the complete error-code lookup table, and an AI-rescue prompt template."
keywords: [widget debug, AI customer service debug, widget connection test, chatbot connection test, HTTP log, cURL examples, AI rescue, debug panel, connection diagnostics, chungtair]
last_updated: 2026-05-13
---

# Connection Debug Panel

> When the widget doesn't respond and the question is "where did it break?", this panel exists so an engineer (or an AI assistant the engineer pastes logs into) can answer it in under a minute.

Languages available below: [繁體中文](#繁體中文) | [English](#english) | [日本語](#日本語)

---

## English

### Why This Panel Exists

When an embedded widget misbehaves, engineers typically ask three things:

1. Did the script load?
2. Did the widget call the backend?
3. Which call failed, and what was the error?

Browser DevTools shows you (1) and (2) — but (3)'s full response body isn't always visible (CORS-trimmed, server-redacted, or just hard to find). This panel sits on the **backend side** of the exchange and surfaces the actual `request` / `response` per endpoint, with full bodies, durations, headers, and origin attribution.

### Where to Find It

1. Sign in to the [client dashboard](https://aibot.chungtair.com/client/login).
2. Open the Web Bot you want to debug.
3. In the "Connection" section, you'll see three buttons: `🔍 Connection Debug`, `Widget Settings`, `Delete Bot`.
4. Click `🔍 Connection Debug` to open the modal.

Debug data **auto-expires within 24 hours** in Redis — no long-term retention of sensitive material.

### What the Panel Looks Like

```text
┌──────────────────────────────────────────────────────────┐
│  🔍 Widget Connection Debug                          ✕  │
├──────────────────────────────────────────────────────────┤
│  Token:                 web_xxxxxxxx                     │
│  Last successful ping:  chungtair.com · 3 minutes ago    │
│                                                            │
│  [⚡ Test Connection]  [📋 Copy full debug]              │
├──────────────────────────────────────────────────────────┤
│  📡 Recent requests (1 per endpoint)                     │
│                                                            │
│  ── 14:32:18  POST /api/widget/message    200  ✅ ──     │
│  Origin:    https://chungtair.com                         │
│  IP:        1.2.3.4                                       │
│  Request:   { "token":"web_abc", "message":"hi" }         │
│  Response:  { "success": true, "reply": "Hello! ..." }    │
│  Duration:  1.2 s                                         │
│  [📋 Copy as cURL]  [📋 Copy as text]                    │
│                                                            │
│  ── 14:32:15  POST /api/widget/ping       200  ✅ ──     │
│  ── 14:32:15  GET  /api/widget/config     200  ✅ ──     │
└──────────────────────────────────────────────────────────┘
```

### "Test Connection" Flow

Click `⚡ Test Connection` and the backend opens a 35-second listening window (30 seconds for you to act + 5 seconds of buffer):

1. The panel shows `Waiting for widget... Xs left`.
2. You open (or refresh) your site in another tab.
3. The widget fires its load-time request, the backend captures it, the panel turns green: `✅ Success! Request from example.com`.
4. If nothing arrives within 30 seconds, a 5-point diagnostic checklist appears.

### Three Copy Buttons — Which One to Use

| Button | Output Format | Best For |
|---|---|---|
| `📋 Copy as cURL` | Single request → curl command | Engineer's terminal, ready to replay |
| `📋 Copy as text` | Single request, all fields plain text | ChatGPT / Claude — ask AI to interpret |
| `📋 Copy full debug` | Token + last ping + all 3 requests | Single paste for engineers / AI, most complete |

### Error Code Lookup Table

| Status | Message | Endpoint | Likely Cause | Fix |
|---|---|---|---|---|
| `200` | `success` | config / ping / message | Everything OK | None |
| `200` | Service paused | message | Bot disabled (`enabled=false`) in dashboard | Enable the bot in your dashboard |
| `400` | `bad json` | ping | Request body is not valid JSON | Check if `widget.js` was tampered with; verify `Content-Type` |
| `400` | `reply: ""` | message | Message empty, not a string, or > 300 chars | Keep messages 1–300 characters |
| `404` | `invalid token` | config / ping | Widget token does not exist or wrong format | Token must be 32 lowercase hex chars; the copy-paste must not be truncated |
| `404` | Widget invalid or deleted | message | Same as invalid token, or the bot was deleted | Re-copy the embed snippet from the dashboard |
| `429` | Rate limited | message | > 20 messages per minute from one IP | Visitor spamming or under attack; check traffic / block the IP |
| `429` | Rate limited | ping | > 30 pings per minute from one IP | Usually a visitor rapidly refreshing; normal usage won't hit this |
| `500` | System error, please retry | message | AI layer error (OpenAI / RAG / handoff write) | Retry shortly; if persistent, check dashboard alerts / contact support |

### AI Rescue Prompt Template

The `📋 Copy full debug` button produces a plain-text format engineered to be pasted into ChatGPT, Claude, or Cursor with this prompt:

```text
I embedded the Chungtair AI customer service widget on my site, but the chat
button doesn't appear / messages aren't sending (replace with actual symptom).

Here's the full log from the dashboard Debug Panel:

[paste "📋 Copy full debug" content here]

Tell me:
1. Which endpoint failed?
2. What's the root cause?
3. What exactly should I do to fix it?
```

The format includes structured fields (Time, Method, URL, Origin, IP, User-Agent, Request, Status, Response, Duration) per request, plus the Token and last successful ping at the top — everything the AI needs to reason about the failure.

---

## 繁體中文

### 為什麼需要 Debug 面板

Widget 嵌入後若沒反應，工程師會問三件事：

1. script 有沒有載到？
2. widget 有沒有呼叫到後端 API？
3. 哪個 API call 失敗、回了什麼錯誤？

瀏覽器 F12 看得到 (1)(2)，但 (3) 的完整 response body 不一定看得到完整原因（CORS 修剪、伺服器隱藏、或就是難找）。這個面板從**後端視角**看 HTTP 互動，給出每個 endpoint 真實的 `request` / `response`，完整 body、耗時、headers、origin 來源都有。

### 在哪裡 / 怎麼打開

1. 登入[客戶後台](https://aibot.chungtair.com/client/login)
2. 進入您要 debug 的 Web Bot
3. 「連線設定」區看到三顆按鈕：`🔍 連線診斷`、`Widget 設定`、`刪除此機器人`
4. 點「🔍 連線診斷」開啟 modal

面板資料**在 Redis 內 24 小時自動過期**，不長期存放敏感資料。

### 面板長相

```text
┌──────────────────────────────────────────────────────────┐
│  🔍 Widget 接線 Debug                                ✕  │
├──────────────────────────────────────────────────────────┤
│  Token:                 web_xxxxxxxx                     │
│  最後一次成功 ping:     chungtair.com · 3 分鐘前         │
│                                                            │
│  [⚡ 立即測試連線]  [📋 複製整份 Debug]                  │
├──────────────────────────────────────────────────────────┤
│  📡 最近的請求（每種 endpoint 各 1 筆）                  │
│                                                            │
│  ── 14:32:18  POST /api/widget/message    200  ✅ ──     │
│  Origin:    https://chungtair.com                         │
│  IP:        1.2.3.4                                       │
│  Request:   { "token":"web_abc", "message":"hi" }         │
│  Response:  { "success": true, "reply": "您好！..." }     │
│  耗時:      1.2 s                                         │
│  [📋 複製為 cURL]  [📋 複製為文字]                       │
│                                                            │
│  ── 14:32:15  POST /api/widget/ping       200  ✅ ──     │
│  ── 14:32:15  GET  /api/widget/config     200  ✅ ──     │
└──────────────────────────────────────────────────────────┘
```

### 「立即測試連線」流程

按下 `⚡ 立即測試連線` 後，後端開 35 秒監聽窗口（30 秒給用戶 + 5 秒緩衝）：

1. 面板顯示「等待 Widget 來打卡... 還剩 X 秒」
2. 你在另一個分頁打開（或重新整理）你的網站
3. Widget 載入時發送請求，後端命中，面板秒變綠：「✅ 連線成功！從 example.com 收到請求」
4. 30 秒內若都沒收到，顯示 5 點診斷清單

### 三種複製按鈕怎麼選

| 按鈕 | 輸出格式 | 貼到哪裡用 |
|---|---|---|
| `📋 複製為 cURL` | 單一請求轉成 curl 命令 | 工程師的終端機，可直接重現 |
| `📋 複製為文字` | 單一請求所有欄位逐行純文字 | ChatGPT / Claude，請 AI 解讀 |
| `📋 複製整份 Debug` | Token + 最後 ping + 全部 3 個請求 | 一鍵貼給工程師 / AI，最完整 |

### 錯誤碼對照表

| Status | 訊息 | Endpoint | 可能原因 | 解法 |
|---|---|---|---|---|
| `200` | `success` | config / ping / message | 一切正常 | 無 |
| `200` | 客服暫停服務中 | message | Bot 在後台被 owner 停用（`enabled=false`） | 到後台啟用該 Bot |
| `400` | `bad json` | ping | Request body 不是合法 JSON | 檢查 `widget.js` 是否被竄改、確認 `Content-Type` |
| `400` | `reply: ""` | message | 訊息為空、不是字串、或超過 300 字 | 訊息控制在 1-300 字 |
| `404` | `invalid token` | config / ping | Widget token 不存在或格式錯 | Token 必須是 32 字小寫 hex；複製貼上不可被截斷 |
| `404` | Widget 設定無效或已被刪除 | message | 同 invalid token，或該 Bot 已被刪 | 重新從後台複製嵌入代碼貼到網站 |
| `429` | 請求過於頻繁 | message | IP 每分鐘 > 20 則訊息 | 訪客洗版或被攻擊；確認流量正常 / 加 IP 黑名單 |
| `429` | 請求過於頻繁 | ping | IP 每分鐘 > 30 ping | 通常是訪客瘋狂重整網頁；正常使用不會觸發 |
| `500` | 系統錯誤，請稍後再試 | message | AI 那層出錯（OpenAI / RAG / handoff write） | 稍候重試；若持續，看後台告警 / 聯絡客服 |

### AI 求救 Prompt 範本

`📋 複製整份 Debug` 產生的純文字格式特別為 AI 設計，可直接貼到 ChatGPT / Claude / Cursor，加上：

```text
我把忠台 AI 客服的 Widget 嵌入代碼貼到我的網站，但聊天按鈕沒出現／訊息送不出去（改成實際症狀）。

以下是後台 Debug 面板的完整 log：

[此處貼上「📋 複製整份 Debug」內容]

請告訴我：
1. 哪個 endpoint 失敗？
2. 失敗的根本原因是什麼？
3. 我具體該怎麼修？
```

格式內含每筆請求的結構化欄位（Time、Method、URL、Origin、IP、User-Agent、Request、Status、Response、Duration），加上頂端的 Token 與最後成功 ping — AI 推理失敗原因所需的資訊一次給齊。

---

## 日本語

### なぜデバッグパネルが必要か

埋め込んだ Widget が反応しないとき、エンジニアは通常 3 つを確認します：

1. script は読み込まれたか？
2. Widget はバックエンド API を呼び出したか？
3. どの API 呼び出しが失敗し、どんなエラーが返ったか？

ブラウザの F12 で (1)(2) は見えますが、(3) のレスポンスボディの全容は必ずしも見えません（CORS でトリミング、サーバー側で隠蔽、または単純に見つけにくい）。このパネルは **バックエンド視点** から HTTP やり取りを表示し、各エンドポイントの実際の `request` / `response`、完全な body、所要時間、headers、origin の出処を提供します。

### どこで開けるか

1. [クライアント管理画面](https://aibot.chungtair.com/client/login)にログイン
2. デバッグしたい Web Bot を開く
3. 「接続設定」欄に「🔍 接続デバッグ」「Widget 設定」「Bot を削除」の 3 つのボタンが表示
4. 「🔍 接続デバッグ」をクリックでデバッグモーダルが開く

デバッグデータは **Redis 内で 24 時間以内に自動で消去** され、機微情報を長期保存しません。

### パネルの構成

```text
┌──────────────────────────────────────────────────────────┐
│  🔍 Widget 接続デバッグ                              ✕  │
├──────────────────────────────────────────────────────────┤
│  Token:                 web_xxxxxxxx                     │
│  最後の成功 ping:       chungtair.com · 3 分前           │
│                                                            │
│  [⚡ 接続テスト]  [📋 デバッグ全体をコピー]              │
├──────────────────────────────────────────────────────────┤
│  📡 最近のリクエスト（エンドポイント別 1 件）            │
│                                                            │
│  ── 14:32:18  POST /api/widget/message    200  ✅ ──     │
│  Origin:    https://chungtair.com                         │
│  IP:        1.2.3.4                                       │
│  Request:   { "token":"web_abc", "message":"hi" }         │
│  Response:  { "success": true, "reply": "こんにちは！..." }│
│  処理時間:  1.2 s                                         │
│  [📋 cURL としてコピー]  [📋 テキストとしてコピー]       │
│                                                            │
│  ── 14:32:15  POST /api/widget/ping       200  ✅ ──     │
│  ── 14:32:15  GET  /api/widget/config     200  ✅ ──     │
└──────────────────────────────────────────────────────────┘
```

### 「接続テスト」の流れ

`⚡ 接続テスト` を押すと、バックエンドは 35 秒のリスニングウィンドウを開く（30 秒ユーザー操作 + 5 秒バッファ）：

1. パネルに「Widget 応答待機... 残り X 秒」と表示
2. 別タブでサイトを開く（または再読み込み）
3. Widget 読み込み時のリクエストが発火、バックエンドがキャッチ、パネルが即座にグリーンに：「✅ 接続成功！example.com からリクエスト受信」
4. 30 秒以内に何も来なければ、5 点の診断チェックリストが表示

### 3 つのコピーボタンの使い分け

| ボタン | 出力形式 | 貼り付け先 |
|---|---|---|
| `📋 cURL としてコピー` | 単一リクエストを curl コマンドに変換 | エンジニアのターミナル、即再現可能 |
| `📋 テキストとしてコピー` | 単一リクエストの全フィールドをプレーンテキストで | ChatGPT / Claude — AI に解釈を依頼 |
| `📋 デバッグ全体をコピー` | Token + 最後の ping + 全 3 リクエスト | エンジニア / AI に一括貼り付け、最も完全 |

### エラーコード対照表

| Status | メッセージ | Endpoint | 考えられる原因 | 解決策 |
|---|---|---|---|---|
| `200` | `success` | config / ping / message | 全て正常 | なし |
| `200` | サービス停止中 | message | 管理画面で Bot が無効化されている（`enabled=false`） | 管理画面で Bot を有効化 |
| `400` | `bad json` | ping | リクエストボディが有効な JSON ではない | `widget.js` が改変されていないか、`Content-Type` を確認 |
| `400` | `reply: ""` | message | メッセージが空、文字列でない、または 300 字超 | メッセージを 1〜300 文字に収める |
| `404` | `invalid token` | config / ping | Widget token が存在しないか形式が不正 | Token は 32 文字の小文字 hex。コピペで切り取られていないか確認 |
| `404` | Widget 設定が無効または削除済み | message | invalid token と同様、または Bot が削除されている | 管理画面から埋め込みコードを再取得して貼り直し |
| `429` | リクエスト過多 | message | 1 分間に同一 IP から 20 件超のメッセージ | 訪問者の連投または攻撃の可能性。トラフィック確認／IP ブロック |
| `429` | リクエスト過多 | ping | 1 分間に同一 IP から 30 回超の ping | 通常は訪問者の連続リロード。通常利用では発生しません |
| `500` | システムエラー、後ほど再試行 | message | AI 層のエラー（OpenAI / RAG / handoff 書き込み） | 少し待って再試行。継続する場合は管理画面アラート確認／サポート連絡 |

### AI 支援プロンプトテンプレート

`📋 デバッグ全体をコピー` が生成するプレーンテキスト形式は AI 向けに設計されており、ChatGPT / Claude / Cursor にそのまま貼り付け、以下を追加：

```text
忠台 AI カスタマーサービスの Widget 埋め込みコードを自サイトに貼りましたが、チャットボタンが表示されません／メッセージが送れません（実際の症状に書き換えてください）。

以下は管理画面デバッグパネルの完全ログです：

[ここに「📋 デバッグ全体をコピー」の内容を貼り付け]

教えてください：
1. どのエンドポイントが失敗しましたか？
2. 失敗の根本原因は何ですか？
3. 具体的にどう修正すればよいですか？
```

形式には各リクエストの構造化フィールド（Time、Method、URL、Origin、IP、User-Agent、Request、Status、Response、Duration）と、上部の Token と最後の成功 ping が含まれます — AI が失敗原因を推論するのに必要なすべての情報が一括で提供されます。

---

## See Also

- [Widget Embed Guide](./widget-embed-guide.md)
- [HTTP API Reference](./widget-api-reference.md)
- [Troubleshooting](./widget-troubleshooting.md)

## How to Cite

> Chung Tair Ltd. (2026). *Connection Debug Panel — Engineer Survival Kit*. Retrieved from <https://github.com/chungtair/chungtair-docs/blob/main/docs/widget-debug-panel.md>. Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
