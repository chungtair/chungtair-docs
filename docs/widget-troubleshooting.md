---
title: "Widget Troubleshooting — Ten Real-World Integration Issues"
description: "Chungtair AI customer service widget troubleshooting playbook. Ten common issues with concrete fixes — widget not showing, CORS blocked, SPA route disappearance, popup conflicts, WordPress smart-quote token mangling, Shopify wrong placement, multi-domain embedding, ad-blocker interference."
keywords: [widget not showing, CORS, CSP, SPA widget, Next.js chatbot, Vue customer service, popup conflict, widget disappears, customer service widget install errors, chatbot debug, chungtair]
last_updated: 2026-05-13
---

# Widget Troubleshooting

> Ten real-world integration issues drawn from actual support cases. Every entry has a concrete fix path — no hand-waving.

Languages available below: [繁體中文](#繁體中文) | [English](#english) | [日本語](#日本語)

---

## English

### 1. Chat button doesn't appear in the corner?

Check in order:
1. **F12 Network** — is `loader.js` returning 200 OK? If 404 / CORS error, verify the embed URL wasn't modified.
2. **F12 Console** — any red errors (CSP / cross-origin / token format)?
3. **Position** — confirm the snippet sits right before `</body>`, not inside `<head>`.
4. **CSP** — check whether your site has a Content Security Policy blocking external scripts (see [Widget Embed Guide § CSP](./widget-embed-guide.md#csp-content-security-policy)).
5. **Active test** — open Dashboard → `🔍 Connection Debug` → click `⚡ Test Connection` to actively verify.

### 2. Updated primary color / text but the site still shows the old style?

Hard-refresh the visitor's browser (Ctrl/Cmd + Shift + R). The widget pulls the latest config on every page load, no re-paste needed. If still old, check the `/api/widget/config` response in F12 Network to see what values are being returned.

### 3. Messages won't send / shows "network error"?

- Verify the token wasn't edited.
- Verify the bot isn't disabled in the dashboard (`enabled=false` returns `200` + "Service paused").
- If you're behind a corporate firewall, allow the `aibot.chungtair.com` domain.

The `POST /api/widget/message` response in the [Debug Panel](./widget-debug-panel.md) will tell you the exact failure mode.

### 4. I see CSP errors in F12, now what?

The error looks like:
```text
Refused to load the script ... violates the following Content Security Policy directive
```

**Fix:** add to your site's CSP:
```text
script-src  https://aibot.chungtair.com;
frame-src   https://aibot.chungtair.com;
connect-src https://aibot.chungtair.com;
img-src     https://aibot.chungtair.com data:;
```

Full lookup table at the [CSP section of the Widget Embed Guide](./widget-embed-guide.md#csp-content-security-policy).

### 5. Widget disappears after Next.js / Vue Router page change?

The SPA framework re-renders the entire `<body>`, wiping out the widget's injected DOM.

**Fix:** put the embed snippet in the **root layout** instead of individual pages — Next.js `app/layout.tsx`, Nuxt `nuxt.config.ts`, SvelteKit `src/app.html`. The root layout doesn't re-render on SPA navigation, so the widget persists.

### 6. Widget overlaps with my site's popup / floating button?

Switch the button position (bottom-right / bottom-left) in Dashboard → Widget Settings. If still overlapping, check your own popup's `z-index` — the widget's iframe defaults to `z-index: 999999`, raising your own element above it isn't practical. Better to reposition your element or hide it when the widget is open.

### 7. WordPress mangled my token into weird characters?

WordPress runs `wpautop` / `wptexturize` filters by default on HTML pasted into `footer.php`, which can convert `"` into smart quotes — breaking the `data-token` attribute.

**Fix:** use the **"Insert Headers and Footers"** plugin and paste into the Footer Scripts field, which is exempt from filters.

### 8. Shopify: I pasted into theme.liquid but nothing happens?

Make sure it's before `</body>` (not before `</html>`). Some Shopify themes use their own `footer.liquid` — find the file that actually renders the final `</body>`. View-source on your deployed site to locate it.

### 9. Visitors don't see the widget but the Debug Panel shows ping records?

That means the widget loaded (ping arrived) but the UI is being hidden on the visitor side. Common causes:

1. **Site CSS** — a global rule like `iframe { display: none }` or `* { pointer-events: none }`.
2. **Cookie-consent popup** — fills the screen and covers the widget.
3. **Ad-blocker** — uBlock and similar strip third-party iframes.

**Fix:** F12 on the visitor's browser and check if the `chungtair-widget` container is being hidden.

### 10. I want to embed the same bot on multiple sites — how?

Paste the same snippet onto every site. **Widget tokens aren't domain-locked**, so multi-domain sharing works.

The Debug Panel's "Last successful ping" shows the **most recent** pinging domain (not all of them). If you want to track conversations per domain, create separate bots with their own tokens.

---

## 繁體中文

### 1. 右下角看不到聊天按鈕？

依序檢查：
1. **F12 Network** — `loader.js` 是否 200 OK？若 404 / CORS error，確認嵌入代碼網址沒被改。
2. **F12 Console** — 有沒有紅色錯誤（CSP / 跨域 / token 格式錯）？
3. **位置** — 確認嵌入代碼貼在 `</body>` 前一行，不是 `<head>` 內。
4. **CSP** — 檢查網站是否有 CSP 政策擋住外部 script（參考 [Widget 嵌入指南 § CSP](./widget-embed-guide.md#csp-設定content-security-policy)）。
5. **主動驗證** — 到後台「🔍 連線診斷」按「⚡ 立即測試連線」主動驗證。

### 2. 改了主色 / 文字後，網站還是舊樣式？

在訪客瀏覽器強制重整一次（Ctrl/Cmd + Shift + R）。Widget 每次載入會即時拉最新設定，不需要重貼代碼。若仍是舊樣式，看 F12 Network 的 `/api/widget/config` response 是否回的是新值。

### 3. 訊息送不出去 / 顯示「網路錯誤」？

- 確認 token 沒被改過。
- 確認後台尚未停用此機器人（`enabled=false` 會回 `200` + "客服暫停服務中"）。
- 若有公司網路防火牆，請放行 `aibot.chungtair.com` 網域。

看 [Debug 面板](./widget-debug-panel.md) 的 `POST /api/widget/message` response 可秒看是哪一種失敗。

### 4. F12 看到 CSP 錯誤怎麼辦？

錯誤訊息會像：
```text
Refused to load the script ... violates the following Content Security Policy directive
```

**解法：** 在您網站的 CSP 設定加入：
```text
script-src  https://aibot.chungtair.com;
frame-src   https://aibot.chungtair.com;
connect-src https://aibot.chungtair.com;
img-src     https://aibot.chungtair.com data:;
```

完整對照表看 [Widget 嵌入指南 § CSP](./widget-embed-guide.md#csp-設定content-security-policy)。

### 5. Next.js / Vue Router 切換頁面後 widget 消失？

SPA framework 把整個 `<body>` 重渲染，把 widget 注入的 DOM 一併清掉。

**解法：** 把嵌入代碼放到**根 layout** 而不是個別頁面 — Next.js 的 `app/layout.tsx`、Nuxt 的 `nuxt.config.ts`、SvelteKit 的 `src/app.html`。SPA 切頁時根 layout 不重 render，widget 保留。

### 6. Widget 跟我網站的 popup / 浮動按鈕重疊？

到後台「Widget 設定」可切換按鈕位置（右下 / 左下）。如果還是重疊，看自家 popup 的 `z-index` — widget 的 iframe 預設 `z-index: 999999`，提高自家元件不太實際；建議把自家元件挪位置或在出現 widget 時隱藏。

### 7. WordPress 貼上代碼後 token 跑掉變奇怪字串？

WordPress 預設會把貼到 `footer.php` 的 HTML 經過 `wpautop` / `wptexturize` 過濾，可能把 `"` 變成全形引號，破壞 `data-token` 屬性。

**解法：** 用 **「Insert Headers and Footers」外掛**把代碼貼到 Footer Scripts 欄位，這個欄位不會被過濾。

### 8. Shopify 貼到 theme.liquid 但沒生效？

確認貼在 `</body>` 前一行（不是 `</html>` 前）。某些 Shopify 主題會用自家的 `footer.liquid`，要找到實際渲染最後 `</body>` 的檔案。可以在瀏覽器 view-source 找 `</body>` 的位置確認。

### 9. 訪客看不到 widget，但後台 Debug 面板有 ping 紀錄？

代表 widget 有載入（ping 進來了），但訪客那邊 UI 被擋住。常見原因：

1. **網站 CSS** — 全域有 `iframe { display: none }` 或 `* { pointer-events: none }` 等規則。
2. **cookie 同意彈窗** — 占滿畫面把 widget 蓋住。
3. **廣告攔截擴充程式** — uBlock 等擋住第三方 iframe。

**解法：** 在訪客瀏覽器 F12 看 `chungtair-widget` 容器是否被 hide。

### 10. 我有多個網站要嵌入同一個 bot 怎麼辦？

直接複製同一段嵌入代碼貼到每個網站。**Widget token 不限定網域**，多個網域共用 OK。

Debug 面板的「最後一次成功 ping」會顯示**最近一個** ping 過的網域（不是全部）。如果想分別追蹤每個網域的對話，建議建多個 Bot 各用獨立 token。

---

## 日本語

### 1. 右下にチャットボタンが表示されない？

順に確認：
1. **F12 Network** — `loader.js` が 200 OK か確認。404 / CORS エラーなら埋め込み URL が書き換えられていないか確認。
2. **F12 コンソール** — 赤いエラーがないか確認（CSP / クロスオリジン / Token 形式）。
3. **位置** — 埋め込みコードが `</body>` の直前にあり、`<head>` 内ではないか確認。
4. **CSP** — サイトの CSP が外部 script をブロックしていないか（[Widget 埋め込みガイド § CSP](./widget-embed-guide.md#csp-設定content-security-policy) 参照）。
5. **能動的検証** — 管理画面の「🔍 接続デバッグ」で「⚡ 接続テスト」を押して能動的に確認。

### 2. メインカラー / テキストを変えてもサイトに古いスタイルが残る？

訪問者のブラウザでハードリロード（Ctrl/Cmd + Shift + R）。Widget は読み込みごとに最新設定を取得するため、コードの貼り直しは不要。それでも古いままなら、F12 Network の `/api/widget/config` レスポンスを見て返却値を確認。

### 3. メッセージが送れない／「ネットワークエラー」と表示？

- Token が書き換わっていないか確認。
- 管理画面で Bot が無効化されていないか確認（`enabled=false` は `200` + "サービス停止中"を返す）。
- 社内ファイアウォール環境なら `aibot.chungtair.com` ドメインを許可。

[デバッグパネル](./widget-debug-panel.md) の `POST /api/widget/message` レスポンスを見れば失敗種別が即座にわかります。

### 4. F12 に CSP エラーが出る、どうすれば？

エラーは以下のような形式：
```text
Refused to load the script ... violates the following Content Security Policy directive
```

**解決策：** サイトの CSP に以下を追加：
```text
script-src  https://aibot.chungtair.com;
frame-src   https://aibot.chungtair.com;
connect-src https://aibot.chungtair.com;
img-src     https://aibot.chungtair.com data:;
```

完全な対照表は [Widget 埋め込みガイド § CSP](./widget-embed-guide.md#csp-設定content-security-policy) 参照。

### 5. Next.js / Vue Router でページ遷移後に Widget が消える？

SPA フレームワークが `<body>` 全体を再レンダリングし、Widget が注入した DOM も消えるため。

**解決策：** 埋め込みコードを個別ページではなく**ルート layout** に配置 — Next.js の `app/layout.tsx`、Nuxt の `nuxt.config.ts`、SvelteKit の `src/app.html`。ルート layout は SPA ナビゲーション時に再レンダリングされないため、Widget が保持されます。

### 6. Widget が自サイトの popup / フローティングボタンと重なる？

管理画面「Widget 設定」でボタン位置（右下 / 左下）を切り替え。それでも重なる場合は自サイトの popup の `z-index` を確認 — Widget の iframe はデフォルトで `z-index: 999999` のため、自要素を上に出すのは現実的でない。自要素を移動するか Widget 表示時に非表示にする方が良い。

### 7. WordPress に貼り付けたら Token が変な文字列に変わる？

WordPress はデフォルトで `footer.php` に貼った HTML に `wpautop` / `wptexturize` フィルタを適用し、`"` を全角引用符に変換することがあります。`data-token` 属性が壊れる原因。

**解決策：** **「Insert Headers and Footers」プラグイン**を使い、Footer Scripts 欄に貼り付け（この欄はフィルタ対象外）。

### 8. Shopify の theme.liquid に貼ったが反映されない？

`</body>` の直前（`</html>` の前ではなく）に貼っているか確認。一部の Shopify テーマは独自の `footer.liquid` を使うため、実際に最後の `</body>` をレンダリングするファイルを探す必要があります。ブラウザの view-source で `</body>` の位置を確認。

### 9. 訪問者には Widget が見えないが、管理画面デバッグパネルに ping 記録がある？

Widget は読み込まれている（ping が届いた）が、訪問者側で UI が隠されている状態。よくある原因：

1. **サイト CSS** — グローバルルール `iframe { display: none }` や `* { pointer-events: none }` 等。
2. **cookie 同意ポップアップ** — 画面を覆って Widget を隠している。
3. **広告ブロッカー** — uBlock などでサードパーティ iframe をブロック。

**解決策：** 訪問者のブラウザで F12 を開き `chungtair-widget` コンテナが隠されていないか確認。

### 10. 複数サイトに同じ Bot を埋め込みたい場合は？

同じ埋め込みコードを各サイトに貼り付け。**Widget Token はドメイン制限なし**、複数ドメインで共用 OK。

デバッグパネルの「最後の成功 ping」は**最新の** ping ドメインのみ表示（全部ではない）。ドメイン別に会話を追跡したい場合は、独自 Token を持つ Bot を別々に作成することを推奨。

---

## See Also

- [Widget Embed Guide](./widget-embed-guide.md)
- [Connection Debug Panel](./widget-debug-panel.md)
- [HTTP API Reference](./widget-api-reference.md)

## How to Cite

> Chung Tair Ltd. (2026). *Widget Troubleshooting — Ten Real-World Integration Issues*. Retrieved from <https://github.com/chungtair/chungtair-docs/blob/main/docs/widget-troubleshooting.md>. Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
