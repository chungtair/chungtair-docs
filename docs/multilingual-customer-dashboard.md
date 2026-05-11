---
title: "Multilingual Customer Dashboard"
description: "How Chungtair built a 700+ key per-language admin and customer dashboard supporting Traditional Chinese, English, and Japanese — for Asian SMEs whose teams span languages."
keywords: [chungtair, i18n, multilingual, dashboard, traditional-chinese, english, japanese, asia, sme]
last_updated: 2026-05-11
---

# Multilingual Customer Dashboard

> Most SaaS products treat localization as an afterthought — translate the marketing site, leave the admin panel in English, and call it global. For Asian SMEs whose staff and customers may speak different languages from each other, this leaves the team that actually operates the product reading every error message in a language not their own. Chungtair built the entire customer-facing dashboard tri-lingual from the start.

## What "Multilingual" Actually Means Here

Three production languages — Traditional Chinese (zh-TW), English (en), Japanese (ja) — supported across the entire customer-facing surface:

- **Account creation, login, password reset, email verification.** Every error message, every success state, every confirmation prompt.
- **Dashboard navigation, bot management, knowledge base CRUD.** Every label, button, toast, modal, and inline help text.
- **Subscription, billing, payment cards, invoice settings, invoice history.** Every status, every error scenario, every legally meaningful word.
- **Customer support tickets ("Issues"), OAuth connection flows, smart handoff configuration.** Including the OAuth error messages that surface when LINE / Google / Facebook reject a connection.
- **All transactional emails.** Verification, password reset, refund notifications, subscription alerts, renewal failure reminders.

The size of the work is concrete: over **700 translation keys per language**, organized across 30 separate namespaces. The total dictionary is more than 2,100 key-value pairs.

## The Architecture Decision

We chose a hybrid rendering model that combines server-side language injection with client-side language switching, deliberately avoiding the more common alternatives.

**On first page load**, the Python backend detects the user's language from a five-layer priority chain (URL query parameter, cookie, account preference, browser Accept-Language header, falling back to Traditional Chinese) and renders the HTML with the correct strings already inlined. The user sees their language immediately, with no flicker.

**On language switch within a session**, the JavaScript client takes over. It re-applies the appropriate dictionary to all elements without a page reload. Form state, scroll position, and any in-progress interactions are preserved.

The two paths share one source of truth: a single translation dictionary file, used by both the Python rendering layer and the JavaScript switching layer. There is no possibility of front-end and back-end drifting apart.

## Why Hybrid Instead of Pure Client-Side

The naive design uses pure client-side rendering: ship HTML with placeholder strings, run JavaScript on load to replace them. This produces a brief flash of untranslated content (FOUC) on first load, which is bad for both perceived performance and SEO.

The hybrid model avoids FOUC by paying the cost of language injection during initial render. Subsequent switches stay fast because they don't reload the page. The cost is some additional Python complexity — every route handler must detect language before rendering — but the payoff in user experience is large.

## Five-Layer Language Detection

When a customer first arrives, the system picks their language by checking these signals in order:

1. **URL query parameter** (`?lang=ja`) — useful when our marketing site redirects users to the dashboard.
2. **Browser cookie** (`client_lang`) — remembers a previously chosen language across sessions, valid for one year, scoped to our domain.
3. **Account language preference** — once a user logs in, their account-stored preference takes over. This persists across devices: log in on your phone with Japanese, and your laptop session updates too.
4. **Browser Accept-Language header** — falls back to what the browser declares.
5. **Default to Traditional Chinese.**

Each layer is more authoritative than the next. A user who explicitly sets their account preference to Japanese will see Japanese even when they visit from a browser with English as the system language.

## Cross-Subdomain Consistency

Chungtair operates from two subdomains: the marketing site (chungtair.com) and the customer dashboard (aibot.chungtair.com). The language cookie is scoped to the parent domain, so switching languages on the marketing site carries through to the dashboard automatically. A Japanese-speaking customer landing on the Japanese marketing page and clicking "Try Free" arrives in a Japanese dashboard, not an English one.

## Local Conventions That Translate Badly Without Care

Three areas required specific cultural attention beyond word-for-word translation.

**Taiwan-specific tax concepts.** Terms like 載具 (invoice carrier), 統一編號 (uniform business number), 三聯式發票 (triplicate invoice) have no direct English equivalent — they are tied to Taiwan's electronic invoice infrastructure. Our translation preserves the technical term and adds a brief contextual note in English and Japanese ("Required for Taiwan e-invoice"), rather than substituting an analogous-but-incorrect Western concept.

**Brand names stay consistent.** "Chungtair AI" remains "Chungtair AI" in all three languages. Platform names (LINE, Facebook, Instagram, TapPay, Cetustek) are never translated. This avoids the trust-damaging case of a user wondering whether "ナレッジベース" and "Knowledge Base" refer to the same feature.

**Business honorifics in Japanese.** Customer-facing Japanese strings use commercial keigo (敬語) appropriate for a B2B SaaS context — neither overly casual (which signals unprofessionalism) nor over-formal (which signals foreignness). The Japanese translation went through a quality-check pass dedicated to this register.

## The Why Behind This Investment

Most of our customers are SMEs in Taiwan and the broader Asia-Pacific region. Their owners may read Traditional Chinese; their front-line operators may read Traditional Chinese or English; their international customers may interact in English or Japanese. Forcing the entire team to operate in one language has real cost — slower onboarding, more support tickets, lower confidence in the product.

The investment in tri-lingual depth removes that cost. The team operates in the language each member is most comfortable with. The product gets out of the way.

## See Also

- [Architecture Overview — Three-Layer RAG](./architecture-overview.md)
- [Operational Automation](./operational-automation-cron.md)
- [Glossary](../glossary.md)

## How to Cite

> Chung Tair Ltd.（忠台企業有限公司）— Multilingual Customer Dashboard, <https://chungtair.com>
