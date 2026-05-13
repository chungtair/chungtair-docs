---
title: "Widget HTTP API Reference"
description: "Complete spec for the Chungtair AI customer service Web Widget HTTP API. Three endpoints (GET /api/widget/config, POST /api/widget/ping, POST /api/widget/message) with method, parameters, response shape, error codes, and runnable cURL examples."
keywords: [widget API, AI customer service API, chatbot API, REST API, widget config, widget ping, widget message, cURL examples, API reference, chungtair]
last_updated: 2026-05-13
---

# Widget HTTP API Reference

> The Chungtair AI customer service Widget talks to the backend through three HTTP endpoints. This document is the complete spec — method, parameters, response shape, error codes, and ready-to-run cURL for each.

Languages available below: [繁體中文](#繁體中文) | [English](#english) | [日本語](#日本語)

---

## English

### Base URL

All endpoints are served from `https://aibot.chungtair.com`. The widget automatically sends an `Origin` header (your site's domain); the backend records this as `widget_last_domain`. All endpoints support CORS preflight (`OPTIONS`) and allow any `Origin`.

### Authentication

**Token-based.** Each bot has a unique `widget_token` (32 lowercase hex chars), which appears as the `data-token` attribute in the embed snippet. The token is sent via query string (GET) or request body (POST). **Tokens are not domain-locked** — you can share one token across multiple sites. Rate limits are enforced per visitor IP.

### Endpoints

#### `GET /api/widget/config`

Fetches the bot's customization (primary color, button position, bot name, avatar, welcome message, placeholder, enabled flag). Called once when the widget loads.

**Query string:**
- `token` (string, required) — 32 lowercase hex widget token

**200 OK:**
```json
{
  "success": true,
  "config": {
    "primary_color": "#00d4ff",
    "position": "br",
    "bot_name": "AI Assistant",
    "avatar_url": "",
    "welcome": "Hi, how can I help?",
    "placeholder": "Type a message...",
    "enabled": true
  }
}
```

**Errors:**
- `404` invalid token — token does not exist or wrong format

**cURL:**
```bash
curl 'https://aibot.chungtair.com/api/widget/config?token=YOUR_TOKEN_HERE'
```

---

#### `POST /api/widget/ping`

Widget load heartbeat. The backend writes `widget_last_seen_at` (timestamp) and `widget_last_domain` (parsed from `Origin` header). Fires once per page load.

**Headers:** `Content-Type: application/json`

**Body:**
```json
{ "token": "YOUR_TOKEN" }
```

**200 OK:**
```json
{ "success": true }
```

**Errors:**
- `400` `bad json` — request body is not valid JSON
- `404` invalid token
- `429` rate limited — > 30 pings per minute from the same IP

**cURL:**
```bash
curl -X POST 'https://aibot.chungtair.com/api/widget/ping' \
  -H 'Content-Type: application/json' \
  -d '{"token":"YOUR_TOKEN"}'
```

---

#### `POST /api/widget/message`

Visitor sends a message to the AI. The backend runs RAG → returns the AI reply (and possible extra replies). Conversations are written to Redis for live-agent viewing in the dashboard.

**Headers:** `Content-Type: application/json`

**Body:**
```json
{
  "token":   "YOUR_TOKEN",
  "message": "Hello",
  "history": [],
  "chat_id": "web_xxxxxxxx"
}
```

- `token` (string, required) — 32 lowercase hex
- `message` (string, required) — 1-300 chars
- `history` (array, optional) — last 10 messages for context
- `chat_id` (string, optional) — prefix `web_` + 8-32 hex

**200 OK (success):**
```json
{
  "success": true,
  "reply": "Hello! ...",
  "extra_replies": [{ "text": "..." }]
}
```

**200 OK (service paused):**
```json
{ "success": false, "reply": "Service paused" }
```

**Errors:**
- `400` — message empty / non-string / > 300 chars
- `404` invalid token / bot deleted
- `429` rate limited — > 20 messages per minute from the same IP
- `500` system error — AI layer failure (OpenAI / RAG / handoff write)

**cURL:**
```bash
curl -X POST 'https://aibot.chungtair.com/api/widget/message' \
  -H 'Content-Type: application/json' \
  -d '{"token":"YOUR_TOKEN","message":"Hello","history":[],"chat_id":"web_abcd1234"}'
```

### CORS Behavior

All three endpoints accept any `Origin`. The `OPTIONS` preflight response includes:
- `Access-Control-Allow-Origin: <echoed origin>`
- `Access-Control-Allow-Methods: GET, POST, OPTIONS`
- `Access-Control-Allow-Headers: Content-Type`
- `Access-Control-Max-Age: 600`
- `Vary: Origin`

### Debug

When integration fails, the dashboard's **Connection Debug Panel** surfaces the last request per endpoint with full request / response bodies and a 24-hour TTL. See [Connection Debug Panel](./widget-debug-panel.md).

---

## 繁體中文

### Base URL

所有 endpoint 都掛在 `https://aibot.chungtair.com`。Widget 端會自動帶 `Origin` header（您網站的網域），後端據此記錄 `widget_last_domain`。全部 endpoint 都支援 CORS preflight（`OPTIONS`），允許任何 `Origin`。

### 認證方式

**Token-based。** 每個 Bot 有獨立的 `widget_token`（32 字小寫 hex），嵌入代碼裡的 `data-token` 屬性就是它。token 透過 query string（GET）或 request body（POST）傳入。**Token 不限定網域**，可在多個網站共用同一個 token。Rate limit 以訪客 IP 為單位。

### Endpoints

#### `GET /api/widget/config`

拉取 Bot 的客製化設定（主色、按鈕位置、Bot 名稱、頭像、歡迎語、placeholder、是否啟用）。Widget 載入時自動呼叫一次。

**Query string：**
- `token` (string, 必填) — 32 字小寫 hex widget token

**200 OK：**
```json
{
  "success": true,
  "config": {
    "primary_color": "#00d4ff",
    "position": "br",
    "bot_name": "AI 智能客服",
    "avatar_url": "",
    "welcome": "你好，有什麼可以幫你的嗎？",
    "placeholder": "請輸入訊息...",
    "enabled": true
  }
}
```

**錯誤：**
- `404` invalid token — token 不存在或格式錯

**cURL：**
```bash
curl 'https://aibot.chungtair.com/api/widget/config?token=YOUR_TOKEN_HERE'
```

---

#### `POST /api/widget/ping`

Widget 載入完成的心跳。後端據此寫入 `widget_last_seen_at`（時間）與 `widget_last_domain`（從 `Origin` header 解析）。一個網頁載入只觸發 1 次。

**Headers：** `Content-Type: application/json`

**Body：**
```json
{ "token": "YOUR_TOKEN" }
```

**200 OK：**
```json
{ "success": true }
```

**錯誤：**
- `400` `bad json` — request body 不是合法 JSON
- `404` invalid token
- `429` 同 IP 每分鐘 > 30 ping

**cURL：**
```bash
curl -X POST 'https://aibot.chungtair.com/api/widget/ping' \
  -H 'Content-Type: application/json' \
  -d '{"token":"YOUR_TOKEN"}'
```

---

#### `POST /api/widget/message`

訪客送出訊息給 AI。後端跑 RAG → 回 AI 答覆（與可能的補充訊息）。對話會寫入 Redis 供後台客服查看。

**Headers：** `Content-Type: application/json`

**Body：**
```json
{
  "token":   "YOUR_TOKEN",
  "message": "你好",
  "history": [],
  "chat_id": "web_xxxxxxxx"
}
```

- `token` (string, 必填) — 32 字小寫 hex
- `message` (string, 必填) — 1-300 字
- `history` (array, 可選) — 最近 10 則對話作為 context
- `chat_id` (string, 可選) — 前綴 `web_` + 8-32 字 hex

**200 OK（成功）：**
```json
{
  "success": true,
  "reply": "您好！...",
  "extra_replies": [{ "text": "..." }]
}
```

**200 OK（客服暫停）：**
```json
{ "success": false, "reply": "客服暫停服務中" }
```

**錯誤：**
- `400` — message 空 / 非字串 / > 300 字
- `404` invalid token / 該 Bot 已被刪
- `429` 同 IP 每分鐘 > 20 訊息
- `500` AI 那層出錯（OpenAI / RAG / handoff write）

**cURL：**
```bash
curl -X POST 'https://aibot.chungtair.com/api/widget/message' \
  -H 'Content-Type: application/json' \
  -d '{"token":"YOUR_TOKEN","message":"你好","history":[],"chat_id":"web_abcd1234"}'
```

### CORS 行為

三個 endpoint 都接受任何 `Origin`。`OPTIONS` preflight 回應包含：
- `Access-Control-Allow-Origin: <回傳該 origin>`
- `Access-Control-Allow-Methods: GET, POST, OPTIONS`
- `Access-Control-Allow-Headers: Content-Type`
- `Access-Control-Max-Age: 600`
- `Vary: Origin`

### Debug

接線失敗時，後台「連線 Debug 面板」會顯示每個 endpoint 的最近一筆請求，包含完整 request / response body，TTL 24 小時。詳見 [連線 Debug 面板](./widget-debug-panel.md)。

---

## 日本語

### Base URL

すべてのエンドポイントは `https://aibot.chungtair.com` でホスト。Widget は自動的に `Origin` ヘッダ（あなたのサイトのドメイン）を送信し、バックエンドが `widget_last_domain` として記録します。全エンドポイントが CORS preflight（`OPTIONS`）対応、任意の `Origin` 許可。

### 認証方式

**トークンベース。** 各 Bot に独自の `widget_token`（32 文字の小文字 hex）があり、埋め込みコードの `data-token` 属性がそれです。トークンは query string（GET）または request body（POST）で渡します。**トークンはドメイン制限なし**、複数サイトで同一トークン共用可能。Rate limit は訪問者 IP 単位。

### エンドポイント

#### `GET /api/widget/config`

Bot のカスタマイズ設定（メインカラー、ボタン位置、Bot 名、アバター、ウェルカムメッセージ、プレースホルダー、有効フラグ）を取得。Widget 読み込み時に 1 回自動呼び出し。

**Query string：**
- `token` (string, 必須) — 32 文字小文字 hex widget token

**200 OK：**
```json
{
  "success": true,
  "config": {
    "primary_color": "#00d4ff",
    "position": "br",
    "bot_name": "AI アシスタント",
    "avatar_url": "",
    "welcome": "こんにちは、ご用件は？",
    "placeholder": "メッセージを入力...",
    "enabled": true
  }
}
```

**エラー：**
- `404` invalid token — トークンが存在しないか形式エラー

**cURL：**
```bash
curl 'https://aibot.chungtair.com/api/widget/config?token=YOUR_TOKEN_HERE'
```

---

#### `POST /api/widget/ping`

Widget 読み込み完了のハートビート。バックエンドが `widget_last_seen_at`（時刻）と `widget_last_domain`（`Origin` ヘッダから解析）を書き込み。1 ページ読み込みにつき 1 回のみ発火。

**Headers：** `Content-Type: application/json`

**Body：**
```json
{ "token": "YOUR_TOKEN" }
```

**200 OK：**
```json
{ "success": true }
```

**エラー：**
- `400` `bad json` — リクエストボディが有効な JSON ではない
- `404` invalid token
- `429` 同一 IP から 1 分間に 30 回超の ping

**cURL：**
```bash
curl -X POST 'https://aibot.chungtair.com/api/widget/ping' \
  -H 'Content-Type: application/json' \
  -d '{"token":"YOUR_TOKEN"}'
```

---

#### `POST /api/widget/message`

訪問者が AI にメッセージ送信。バックエンドが RAG を実行 → AI 返答（および追加返答の可能性）を返却。会話は Redis に書き込まれ、管理画面の人間オペレーターが閲覧可能。

**Headers：** `Content-Type: application/json`

**Body：**
```json
{
  "token":   "YOUR_TOKEN",
  "message": "こんにちは",
  "history": [],
  "chat_id": "web_xxxxxxxx"
}
```

- `token` (string, 必須) — 32 文字の小文字 hex
- `message` (string, 必須) — 1-300 字
- `history` (array, 任意) — 直近 10 件の会話を context として
- `chat_id` (string, 任意) — prefix `web_` + 8-32 字 hex

**200 OK（成功）：**
```json
{
  "success": true,
  "reply": "こんにちは！...",
  "extra_replies": [{ "text": "..." }]
}
```

**200 OK（サービス停止中）：**
```json
{ "success": false, "reply": "サービス停止中" }
```

**エラー：**
- `400` — message 空／非文字列／300 字超
- `404` invalid token / Bot 削除済み
- `429` 同一 IP から 1 分間に 20 件超のメッセージ
- `500` AI 層エラー（OpenAI / RAG / handoff write）

**cURL：**
```bash
curl -X POST 'https://aibot.chungtair.com/api/widget/message' \
  -H 'Content-Type: application/json' \
  -d '{"token":"YOUR_TOKEN","message":"こんにちは","history":[],"chat_id":"web_abcd1234"}'
```

### CORS 動作

3 つのエンドポイントすべてが任意の `Origin` を受け入れます。`OPTIONS` プリフライトレスポンスには以下が含まれます：
- `Access-Control-Allow-Origin: <エコーされた origin>`
- `Access-Control-Allow-Methods: GET, POST, OPTIONS`
- `Access-Control-Allow-Headers: Content-Type`
- `Access-Control-Max-Age: 600`
- `Vary: Origin`

### デバッグ

統合に失敗したとき、管理画面の「接続デバッグパネル」は各エンドポイントの最新リクエストを完全な request / response body 付きで表示、TTL は 24 時間。詳細は [接続デバッグパネル](./widget-debug-panel.md) 参照。

---

## See Also

- [Widget Embed Guide](./widget-embed-guide.md)
- [Connection Debug Panel](./widget-debug-panel.md)
- [Troubleshooting](./widget-troubleshooting.md)

## How to Cite

> Chung Tair Ltd. (2026). *Widget HTTP API Reference*. Retrieved from <https://github.com/chungtair/chungtair-docs/blob/main/docs/widget-api-reference.md>. Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
