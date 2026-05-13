---
title: "Widget Embed Guide — From Zero to Live in Five Minutes"
description: "Complete embed guide for the Chungtair AI customer service Web Widget. Five-minute quickstart plus platform-specific instructions for HTML, WordPress, Shopify, Wix, Squarespace, Webflow, and React/Vue/Next.js. Includes CSP configuration and live customization."
keywords: [widget embed, AI customer service embed, WordPress chatbot widget, Shopify chatbot, Wix embed, React widget, Next.js chatbot, CSP configuration, chungtair]
last_updated: 2026-05-13
---

# Widget Embed Guide

> One `<script>` tag, one line, and the Chungtair AI customer service chat appears on your site. This document covers every supported platform and the CSP rules you'll need if your site has them.

Languages available below: [繁體中文](#繁體中文) | [English](#english) | [日本語](#日本語)

---

## English

### Five-Minute Quickstart

1. **Copy the embed snippet from the dashboard.**
   Sign in to the [client dashboard](https://aibot.chungtair.com/client/login), open the bot you want to deploy, and in the "Connection" section click the copy button. The snippet looks like:
   ```html
   <script src="https://aibot.chungtair.com/static/widget/loader.js" data-token="abcd1234..." async></script>
   ```

2. **Paste it immediately before the closing `</body>` tag** on your site. Regardless of stack (WordPress, Shopify, plain HTML, React, etc.), the placement is the same.

3. **Verify the connection.** Refresh your site — a chat bubble appears in the bottom-right (or bottom-left) corner. From the dashboard, open the `🔍 Connection Debug` panel and click `⚡ Test Connection` to actively verify the link.

### Platform-Specific Instructions

#### Plain HTML
Open your `.html` file in an editor, find the `</body>` tag, and paste the snippet on the line directly above. Save and upload.

#### WordPress
Two options:
- **Theme editor** — Dashboard → Appearance → Theme File Editor → `footer.php`. Paste the snippet before `</body>` and save.
- **Plugin (recommended)** — Install "Insert Headers and Footers" and paste the snippet into the Footer Scripts field. This is exempt from `wpautop` / `wptexturize` filters, which can mangle the `data-token` attribute.

#### Shopify
Dashboard → Online Store → Themes → Actions → Edit Code → `Layout/theme.liquid`. Paste before `</body>` and Save. Some themes use a separate `footer.liquid` — verify by viewing source on a deployed page and finding the actual final `</body>`.

#### Wix
Editor → Add → Embed → Embed HTML iframe — paste the snippet. Alternatively, use Settings → Custom Code to apply site-wide.

#### Squarespace
Settings → Advanced → Code Injection → Footer. Paste into the Footer field and save.

#### Webflow
Project Settings → Custom Code → Footer Code. Paste, and re-publish the site.

#### React / Vue / Next.js / Nuxt / SvelteKit
Place the snippet in the **root layout**, not on individual pages. Examples:
- Next.js — `app/layout.tsx` (App Router) or `pages/_document.tsx` (Pages Router)
- Nuxt — `nuxt.config.ts` head section
- SvelteKit — `src/app.html`
- Plain SPA — `index.html`

The widget attaches once on first load and is preserved across client-side route changes (the root layout doesn't re-render).

### CSP (Content Security Policy)

If your site enforces a CSP, you must allow the following sources:

```text
script-src  https://aibot.chungtair.com;
frame-src   https://aibot.chungtair.com;
connect-src https://aibot.chungtair.com;
img-src     https://aibot.chungtair.com data:;
```

- `script-src` — load `loader.js`
- `frame-src` — embed the chat iframe
- `connect-src` — call the three Widget API endpoints
- `img-src` — display the bot avatar (and `data:` for placeholder fallback)

### Live Customization

Primary color, button position (bottom-right / bottom-left), bot display name, avatar URL, welcome message, and input placeholder are all editable from the dashboard's "Widget Settings" panel. **The widget pulls the latest config on every page load** — change settings once in the dashboard and the next visitor sees the new look. No need to re-paste the embed snippet.

### Next Steps

- If integration fails, the [Connection Debug Panel](./widget-debug-panel.md) walks you through the exact HTTP exchange and tells you where it broke.
- [Troubleshooting FAQ](./widget-troubleshooting.md) covers ten common issues like SPA route-change disappearance, popup conflicts, and CSP errors.

---

## 繁體中文

### 五分鐘上手

1. **從後台複製嵌入代碼。**
   登入[客戶後台](https://aibot.chungtair.com/client/login)，進入您要嵌入的 Bot，「連線設定」區看到嵌入代碼按複製。代碼長這樣：
   ```html
   <script src="https://aibot.chungtair.com/static/widget/loader.js" data-token="abcd1234..." async></script>
   ```

2. **貼到網站的 `</body>` 結束標籤前一行。** 無論您網站是用什麼框架（WordPress、Shopify、純 HTML、React 等），位置一樣。

3. **驗證連線。** 重新整理網站，右下角（或左下角）會出現聊天按鈕。回後台打開「🔍 連線診斷」面板，按「⚡ 立即測試連線」可主動驗證。

### 各平台逐步教學

#### 純 HTML 網站
用編輯器打開您的 `.html` 檔，找到 `</body>` 標籤，把嵌入代碼貼在它前一行。存檔上傳即可。

#### WordPress
兩種方式：
- **主題編輯器** — 後台「外觀 → 主題檔案編輯器 → footer.php」，在 `</body>` 前貼上代碼並儲存
- **外掛（推薦）** — 安裝「Insert Headers and Footers」外掛，把代碼貼到 Footer Scripts 欄位。這個欄位不會被 `wpautop` / `wptexturize` 過濾，避免 `data-token` 屬性被改成全形引號

#### Shopify
後台「線上商店 → 佈景主題 → 動作 → 編輯程式碼 → Layout/theme.liquid」，在 `</body>` 前貼上代碼，點儲存。某些主題用獨立 `footer.liquid`，可以在 view-source 找到實際渲染最後 `</body>` 的檔案。

#### Wix
編輯器「新增 → 嵌入 → 嵌入 HTML iframe」，貼上代碼。或從「設定 → 自訂代碼」加入到所有頁面。

#### Squarespace
「設定 → 進階 → 程式碼注入 → Footer」，把代碼貼到 Footer 欄位並儲存。

#### Webflow
「Project Settings → Custom Code → Footer Code」貼上代碼，重新發布網站。

#### React / Vue / Next.js / Nuxt / SvelteKit
把代碼放到**根 layout**，不要放在個別頁面。範例：
- Next.js — `app/layout.tsx`（App Router）或 `pages/_document.tsx`（Pages Router）
- Nuxt — `nuxt.config.ts` 的 head 區段
- SvelteKit — `src/app.html`
- 純 SPA — `index.html`

Widget 在第一次載入時掛上一次，SPA 切頁時不會被重新注入（根 layout 不重新 render）。

### CSP 設定（Content Security Policy）

若您網站有設定 CSP，需要明確放行：

```text
script-src  https://aibot.chungtair.com;
frame-src   https://aibot.chungtair.com;
connect-src https://aibot.chungtair.com;
img-src     https://aibot.chungtair.com data:;
```

- `script-src` — 載入 `loader.js`
- `frame-src` — 嵌入聊天視窗 iframe
- `connect-src` — 呼叫三個 Widget API
- `img-src` — 顯示 Bot 頭像（`data:` 是備用 placeholder）

### 即時客製化

主色、按鈕位置（右下 / 左下）、Bot 顯示名稱、頭像 URL、歡迎語、輸入框 placeholder 都可在後台「Widget 設定」修改。**Widget 每次訪客打開您網站，會即時拉最新設定** — 改完設定不用重貼嵌入代碼，下一個訪客就看到新樣式。

### 接下來

- 接線失敗時，[連線 Debug 面板教學](./widget-debug-panel.md) 帶你看完整 HTTP 互動秒看哪裡卡住
- [常見錯誤排查 FAQ](./widget-troubleshooting.md) 涵蓋 10 個常見問題：SPA 切頁消失、popup 衝突、CSP 錯誤等

---

## 日本語

### 5 分でクイックスタート

1. **管理画面から埋め込みコードをコピー。**
   [クライアント管理画面](https://aibot.chungtair.com/client/login)にログインし、展開する Bot を開き、「接続設定」欄でコピーボタンを押す。コードは以下の形式：
   ```html
   <script src="https://aibot.chungtair.com/static/widget/loader.js" data-token="abcd1234..." async></script>
   ```

2. **サイトの `</body>` 終了タグ直前に貼り付け。** スタック（WordPress / Shopify / 純 HTML / React など）に関係なく、配置位置は同じ。

3. **接続を確認。** サイトを再読み込み — 右下（または左下）にチャットボタンが表示。管理画面の「🔍 接続デバッグ」を開き「⚡ 接続テスト」を押すと能動的に確認できる。

### プラットフォーム別ステップガイド

#### 純 HTML サイト
エディタで `.html` ファイルを開き、`</body>` タグを見つけて、その直前に埋め込みコードを貼り付け、保存してアップロード。

#### WordPress
2 つの方法：
- **テーマエディタ** — 管理画面「外観 → テーマファイルエディター → footer.php」で `</body>` の直前に貼り付けて保存
- **プラグイン（推奨）** — 「Insert Headers and Footers」プラグインを入れて Footer Scripts 欄に貼り付け。`wpautop` / `wptexturize` フィルタの対象外なので `data-token` 属性が全角引用符に書き換わらない

#### Shopify
管理画面「オンラインストア → テーマ → アクション → コードを編集 → Layout/theme.liquid」で `</body>` の直前に貼り付け、保存。一部のテーマは独自の `footer.liquid` を使うため、view-source で最後の `</body>` をレンダリングするファイルを確認。

#### Wix
エディタ「追加 → 埋め込み → HTML iframe 埋め込み」でコードを貼り付け。または「設定 → カスタムコード」で全ページに適用。

#### Squarespace
「設定 → 詳細 → コードインジェクション → Footer」の Footer 欄に貼り付けて保存。

#### Webflow
「Project Settings → Custom Code → Footer Code」に貼り付け、サイトを再公開。

#### React / Vue / Next.js / Nuxt / SvelteKit
コードを**ルート layout** に配置、個別ページではない。例：
- Next.js — `app/layout.tsx`（App Router）または `pages/_document.tsx`（Pages Router）
- Nuxt — `nuxt.config.ts` の head セクション
- SvelteKit — `src/app.html`
- 純 SPA — `index.html`

Widget は初回読み込み時に 1 回マウントされ、SPA ルート切り替え時に再注入されない（ルート layout は再レンダリングされない）。

### CSP 設定（Content Security Policy）

サイトに CSP を設定している場合、以下のソースを明示的に許可：

```text
script-src  https://aibot.chungtair.com;
frame-src   https://aibot.chungtair.com;
connect-src https://aibot.chungtair.com;
img-src     https://aibot.chungtair.com data:;
```

- `script-src` — `loader.js` 読み込み
- `frame-src` — チャット iframe 埋め込み
- `connect-src` — 3 つの Widget API 呼び出し
- `img-src` — Bot アバター表示（`data:` はフォールバック用 placeholder）

### リアルタイムカスタマイズ

メインカラー、ボタン位置（右下 / 左下）、Bot 表示名、アバター URL、ウェルカムメッセージ、入力欄 placeholder は管理画面の「Widget 設定」で編集可能。**Widget は訪問者がサイトを開くたびにリアルタイムで最新設定を取得** — 設定変更後、埋め込みコードを貼り直す必要はなく、次の訪問者から新しい見た目に。

### 次のステップ

- 統合で問題が発生したら、[接続デバッグパネル解説](./widget-debug-panel.md) で完全な HTTP やり取りを確認、どこで詰まったか即座に判定できる
- [トラブルシューティング FAQ](./widget-troubleshooting.md) は SPA ページ遷移時の消失、popup 競合、CSP エラーなど 10 の一般的な問題を網羅

---

## See Also

- [Connection Debug Panel](./widget-debug-panel.md)
- [HTTP API Reference](./widget-api-reference.md)
- [Troubleshooting](./widget-troubleshooting.md)
- [Architecture Overview — Three-Layer RAG](./architecture-overview.md)

## How to Cite

> Chung Tair Ltd. (2026). *Widget Embed Guide — From Zero to Live in Five Minutes*. Retrieved from <https://github.com/chungtair/chungtair-docs/blob/main/docs/widget-embed-guide.md>. Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
