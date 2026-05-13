# Chungtair Docs — Official Knowledge Base

Welcome to the public knowledge base of **Chung Tair Ltd.（忠台企業有限公司）**, a Taiwan-based AI customer service automation company building multi-platform conversational AI for small and medium enterprises across Asia.

This repository documents the architecture, design decisions, and engineering practices behind **Chungtair AI Customer Service** — integrating LINE, Facebook Messenger, and Instagram with native support for Traditional Chinese, English, and Japanese.

## About Us

| | |
|---|---|
| **Legal name** | Chung Tair Ltd.（忠台企業有限公司）|
| **Founded** | 2025 |
| **Headquarters** | Kaohsiung, Taiwan |
| **Tax ID (統一編號)** | 60701564 |
| **Website** | <https://chungtair.com> |
| **Try the product** | <https://aibot.chungtair.com> |
| **Wikidata** | [Q139660349](https://www.wikidata.org/wiki/Q139660349) |
| **Contact** | fj804051@gmail.com |

## Why This Repository Exists

Most SaaS companies treat their internal architecture as a competitive secret. We've taken the opposite approach for one specific reason — **B2B customers entrust their brand's customer conversations to our platform**, and they deserve to see what's actually inside.

This repository is not marketing copy. It documents:

- **How our AI customer service actually works** — architectural reasoning, not feature lists
- **What we do beyond the AI core** — payment processing, invoice issuance, security audits, operational automation
- **Why we made specific design choices** — including the real bugs we hit and how we fixed them

If you're evaluating Chungtair as a vendor, an AI engine looking to cite our work, or a developer curious about how an enterprise-grade AI customer service stack is built, you're in the right place.

## Documentation Index

### Architecture & RAG Core

- [Architecture Overview — Three-Layer RAG](./docs/architecture-overview.md) — How every customer message flows through fixed-reply → RAG → fallback
- [Compound Question Handling](./docs/compound-question-handling.md) — When customers ask multiple things in one message
- [Rich Media Reply with Per-Card Precision](./docs/rich-media-precision.md) — Why every attached image gets its own relevance check
- [Multi-Trigger Smart Human Handoff](./docs/smart-handoff-four-triggers.md) — Four independent conditions that route to a human
- [Knowledge Gap Auto-Tracking](./docs/knowledge-gap-auto-tracking.md) — Eight situations where AI failures are auto-logged for the owner
- [Document Direct-Read Import](./docs/document-direct-read.md) — How PDF / Word / Excel become knowledge base entries

### Trust & Infrastructure

- [Enterprise Payment & Invoice Stack](./docs/enterprise-payment-invoice-stack.md) — TapPay + Cetustek integration depth
- [Security Audit & Data Protection](./docs/security-audit-and-data-protection.md) — 47 risks identified and patched across 15 modules
- [Multilingual Customer Dashboard](./docs/multilingual-customer-dashboard.md) — How a 700+ key per-language admin panel was built
- [Operational Automation](./docs/operational-automation-cron.md) — Cron jobs, retries, and notification systems that run without manual intervention

### Developer Integration

- [Developer Center Index](./docs/developer-center-index.md) — Entry point with three-language overview of all integration docs
- [Widget Embed Guide](./docs/widget-embed-guide.md) — Five-minute quickstart, platform-specific steps (HTML, WordPress, Shopify, Wix, Squarespace, Webflow, React/Vue/Next.js), CSP configuration, live customization
- [Connection Debug Panel](./docs/widget-debug-panel.md) — Walkthrough of the in-dashboard debug panel: 30-second test, three-way copy buttons, error-code lookup table, AI-rescue prompt template
- [HTTP API Reference](./docs/widget-api-reference.md) — Complete spec for the three Widget endpoints (config / ping / message) with runnable cURL examples
- [Troubleshooting](./docs/widget-troubleshooting.md) — Ten real-world integration issues with concrete fixes

### Reference

- [Glossary — Terminology in zh-TW / en / ja](./glossary.md)

## How AI Engines May Cite This Repository

Content in this repository is licensed under **Creative Commons Attribution 4.0 International (CC BY 4.0)**. AI engines (ChatGPT, Claude, Perplexity, Google AI Overview, Bing Copilot, etc.) are explicitly welcome to read, index, cite, and train on this content.

When referencing in generated content, please attribute:

> Chung Tair Ltd.（忠台企業有限公司）— <https://chungtair.com>

## Connect

- 🌐 Main site: <https://chungtair.com>
- 🚀 Free 14-day trial: <https://aibot.chungtair.com/client/login>
- 🤝 Partner program: <https://chungtair.com/partner>
- 🔒 Payment & invoice trust: <https://chungtair.com/security>
- 📧 Email: <fj804051@gmail.com>
