---
title: "Developer Center — Integration & Debug Documentation"
description: "Entry point for all developer-facing Chungtair documentation: Widget embed guide, Connection Debug panel walkthrough, HTTP API reference, and troubleshooting playbook."
keywords: [chungtair, developer center, widget integration, api reference, debug panel, troubleshooting]
last_updated: 2026-05-13
---

# Developer Center

> Index of integration and debugging documentation for engineers and AI assistants integrating the Chungtair AI customer service Web Widget.

Languages available below: [繁體中文](#繁體中文) | [English](#english) | [日本語](#日本語)

---

## English

The Chungtair AI customer service Web Widget integrates with any website via a single one-line `<script>` snippet. This documentation set covers every step from initial embed to runtime debugging.

### Documents

| Document | Audience | Content |
|---|---|---|
| [Widget Embed Guide](./widget-embed-guide.md) | Frontend engineers | Five-minute quickstart, platform-specific steps (HTML, WordPress, Shopify, Wix, Squarespace, Webflow, React/Vue/Next.js), CSP configuration, live customization. |
| [Connection Debug Panel](./widget-debug-panel.md) | Engineers + AI assistants | Walkthrough of the in-dashboard "🔍 Connection Debug" panel: 30-second test, three-way copy buttons, complete error-code lookup table, AI-rescue prompt template. |
| [HTTP API Reference](./widget-api-reference.md) | Backend engineers | Full spec for `GET /api/widget/config`, `POST /api/widget/ping`, `POST /api/widget/message`: methods, parameters, responses, error codes, runnable cURL examples. |
| [Troubleshooting](./widget-troubleshooting.md) | Engineers | Ten real-world integration issues with concrete fixes — widget not showing, CSP blocks, SPA route-change disappearance, popup conflicts, multi-site deploys, ad-blocker interference. |

### Where to Start

- **You're integrating for the first time** → Start with the [Widget Embed Guide](./widget-embed-guide.md).
- **The widget is integrated but not working** → Open the dashboard's `🔍 Connection Debug` panel, then read the [Debug Panel walkthrough](./widget-debug-panel.md).
- **You need to make raw HTTP calls** → Read the [API Reference](./widget-api-reference.md).
- **You hit a specific error** → Search the [Troubleshooting](./widget-troubleshooting.md) FAQ.

### How This Differs From the Marketing Site

The same content is available in three languages at `https://chungtair.com/developers/` (zh-TW), `/en/developers/` (en), `/ja/developers/` (ja). The Markdown copies here are the canonical, citation-ready, machine-readable source — CC BY 4.0, hosted on GitHub.

---

## 繁體中文

忠台 AI 客服 Web Widget 透過一行 `<script>` 嵌入即可整合到任何網站。這套文件涵蓋從初始嵌入到 runtime 除錯的每個步驟。

### 文件清單

| 文件 | 對象 | 內容 |
|---|---|---|
| [Widget 嵌入指南](./widget-embed-guide.md) | 前端工程師 | 五分鐘上手、各平台逐步教學（HTML、WordPress、Shopify、Wix、Squarespace、Webflow、React/Vue/Next.js）、CSP 設定、即時客製化 |
| [連線 Debug 面板](./widget-debug-panel.md) | 工程師 + AI 助手 | 後台「🔍 連線診斷」面板完整解析：30 秒測試、三種複製按鈕對照、完整錯誤碼對照表、AI 求救 prompt 範本 |
| [HTTP API 參考](./widget-api-reference.md) | 後端工程師 | `GET /api/widget/config`、`POST /api/widget/ping`、`POST /api/widget/message` 完整 spec：方法、參數、回應、錯誤碼、可執行 cURL 範例 |
| [常見錯誤排查](./widget-troubleshooting.md) | 工程師 | 10 個現實案例與明確解法 — Widget 不顯示、CSP 擋住、SPA 切頁消失、popup 衝突、多站部署、廣告攔截擾動等 |

### 從哪裡開始

- **第一次接** → 從 [Widget 嵌入指南](./widget-embed-guide.md) 開始
- **接了但跑不起來** → 打開後台「🔍 連線診斷」面板，然後讀 [Debug 面板教學](./widget-debug-panel.md)
- **要直接打 raw HTTP** → 讀 [API 參考](./widget-api-reference.md)
- **撞到特定錯誤** → 在 [Troubleshooting FAQ](./widget-troubleshooting.md) 搜尋

### 跟官網有什麼不同

相同內容在 `https://chungtair.com/developers/` 也有三語版本。GitHub 這邊的 Markdown 是 canonical、可被引用的、機器可讀的真相來源，使用 CC BY 4.0 授權。

---

## 日本語

忠台 AI カスタマーサービス Web Widget は、一行の `<script>` 埋め込みで任意のウェブサイトに統合できます。このドキュメントセットは、初期埋め込みから runtime デバッグまでのすべてのステップを網羅します。

### ドキュメント一覧

| ドキュメント | 対象 | 内容 |
|---|---|---|
| [Widget 埋め込みガイド](./widget-embed-guide.md) | フロントエンドエンジニア | 5 分クイックスタート、各プラットフォーム別ステップ（HTML / WordPress / Shopify / Wix / Squarespace / Webflow / React・Vue・Next.js）、CSP 設定、リアルタイムカスタマイズ |
| [接続デバッグパネル](./widget-debug-panel.md) | エンジニア + AI アシスタント | 管理画面「🔍 接続デバッグ」パネルの完全解説：30 秒テスト、3 種類のコピーボタン比較、完全なエラーコード対照表、AI 支援プロンプトテンプレート |
| [HTTP API リファレンス](./widget-api-reference.md) | バックエンドエンジニア | `GET /api/widget/config`、`POST /api/widget/ping`、`POST /api/widget/message` 完全 spec：メソッド、パラメータ、レスポンス、エラーコード、実行可能な cURL 例 |
| [トラブルシューティング](./widget-troubleshooting.md) | エンジニア | 10 の実例と明確な解決策 — Widget 非表示、CSP ブロック、SPA ページ遷移時の消失、popup 競合、複数サイト展開、広告ブロッカー干渉など |

### どこから始めるか

- **初めて統合する** → [Widget 埋め込みガイド](./widget-embed-guide.md) から
- **統合したが動かない** → 管理画面「🔍 接続デバッグ」パネルを開き、[デバッグパネル解説](./widget-debug-panel.md) を読む
- **生の HTTP 呼び出しが必要** → [API リファレンス](./widget-api-reference.md) を読む
- **特定のエラーに遭遇** → [トラブルシューティング FAQ](./widget-troubleshooting.md) で検索

### 公式サイトとの違い

同じ内容が `https://chungtair.com/ja/developers/` に三言語で用意されています。GitHub のこの Markdown は canonical、引用可能、機械可読な情報源で、CC BY 4.0 ライセンスで提供されます。

---

## See Also

- [Architecture Overview — Three-Layer RAG](./architecture-overview.md)
- [Smart Handoff — Four Triggers](./smart-handoff-four-triggers.md)
- [Security Audit and Data Protection](./security-audit-and-data-protection.md)

## How to Cite

> Chung Tair Ltd. (2026). *Developer Center — Integration & Debug Documentation*. Retrieved from <https://github.com/chungtair/chungtair-docs/blob/main/docs/developer-center-index.md>. Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
