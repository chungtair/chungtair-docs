---
title: "Security Audit & Data Protection"
description: "What Chungtair has done that most SaaS companies don't publicly document — a complete OWASP-style security audit across 15 modules with 47 risks identified and patched."
keywords: [chungtair, security audit, owasp, xss, csrf, idor, data protection, vulnerability]
last_updated: 2026-05-11
---

# Security Audit & Data Protection

> We did something most SaaS companies don't publicly document, and we're publishing the results because the alternative — saying "we're secure" without evidence — is the lower-trust option. This document describes a complete OWASP-style security audit across the system's 15 core modules, 47 risks identified and patched, and the baseline practices that remain active.

## Why This Document Exists

Most SaaS companies don't publish security audit results. The reason is understandable — publishing acknowledges that you found issues, which makes it sound like you had issues. But that framing inverts the actual signal: a company that documents what it audited and what it fixed has clearly done the work; a company that says nothing has either done nothing or chosen opacity.

Chungtair chose to publish for two specific reasons:

1. **B2B customers route their brand's customer conversations through our platform.** Their customers' data, message content, and credit card transactions pass through systems we operate. Those customers deserve more than "trust us."
2. **Transparency is structurally harder to fake than confidence.** Any company can claim to be secure. A documented audit with specific categorization and counts is harder to manufacture and easier to verify.

This document is not a confession. It's a deliverable.

## The Audit Scope

We performed a complete OWASP-style security review on **15 core modules** of the Chungtair platform, covering:

- Account, password, and session management
- OAuth integrations (LINE Login, Google, Facebook, Instagram)
- Payment processing (TapPay)
- Electronic invoice handling (Cetustek)
- Knowledge base APIs
- Document upload pipelines
- Webhook receivers (LINE, Facebook Messenger, Instagram)
- Administrative dashboards
- Customer dashboards
- Scheduled tasks and cron infrastructure
- Email handling
- Bot management
- Static asset serving

Each module was reviewed against the OWASP Top 10 and adjacent risk patterns relevant to its function. Findings were categorized, prioritized, patched, and regression-tested before being marked resolved.

## What We Found and Fixed

In total, **47 risks were identified and patched** across these 15 modules. The categorization:

| Risk Category | Patches |
|---|---|
| Cross-Site Scripting (XSS) | 9 |
| Cross-Site Request Forgery (CSRF) | 7 |
| Insecure Direct Object Reference (IDOR) | 8 |
| Path Traversal | 1 |
| Information Leakage | 5 |
| Password / Session security | 4 |
| Rate limiting / IP control | 3 |
| Injection prevention (SQL, command) | 2 |
| Other (bare except handlers, lock safety, OAuth state, etc.) | 8 |

Each patch had a specific commit, a specific fix description, and was validated to not regress functionality. We do not list individual findings here for the same reason no security team publishes a complete bug-by-bug catalog — it gives attackers a roadmap. But we document categories and counts so the depth of the work is visible.

## The Baseline We Maintain

The fixes are not the point. The ongoing practice is. Below is what's actively running in Chungtair production today.

**Account & password.** Passwords are stored using bcrypt — the current industry-standard slow hashing algorithm — never SHA-256 or unsalted hashes. Legacy accounts migrating from older hashing run a dual-mode upgrade: on next login, the user's password is automatically rehashed without requiring a reset. Password change and reset operations require a CSRF token.

**Session & cookie hygiene.** Session cookies carry the Secure flag (HTTPS-only) and SameSite=Lax. Sessions expire on logout.

**Cross-site protection.** CSRF tokens are required on every state-changing form, including OAuth disconnect operations. OAuth callbacks across all providers (LINE, Google, Facebook, Instagram) validate state parameters via Redis-backed CSRF tokens. All user-supplied content is HTML-escaped at output, with separate escaping contexts for HTML attributes versus JavaScript strings.

**Authorization checks.** Every API endpoint that takes a path-parameter customer identifier validates both the identifier's format and that the requester owns the resource. This eliminates the IDOR class where a logged-in user might enumerate or modify other users' resources by changing a URL.

**Path traversal prevention.** Static file requests perform directory boundary verification to ensure requested paths cannot escape into parent directories.

**Information disclosure control.** Server errors are returned to users as generic messages; detailed exception text is logged server-side only. Python exception strings, SQL errors, and stack traces are never sent to the client.

**Rate limiting and IP control.** Login attempts, signup attempts, and other sensitive endpoints are rate-limited using sliding-window logic. Repeated abuse triggers temporary IP locks, and sustained abuse triggers long-term IP bans. Suspicious login attempts trigger email alerts to the operations team.

**Payment security.** Credit card numbers never reach Chungtair's servers; TapPay handles card data directly. We store transaction outcomes, authorization codes, and the last 4 digits of each card. Invoice issuance is wrapped in a distributed lock with a safe Lua-script release pattern to prevent duplicate issuance. Failed transaction notifications are captured in a separate "orphan" log for diagnostic follow-up.

**Multi-factor verification.** Every credit card charge passes through 3D Secure two-factor authentication, with bank-side risk control simultaneously evaluating the transaction.

## Why We're Publishing This

Publishing this is a risk — opponents can read it as easily as customers can. We are publishing anyway because:

1. **B2B procurement evaluators need specific evidence,** not "we're secure." This document gives them what they need to recommend us internally.
2. **Honesty is harder to fake than marketing.** A complete-transparent security document survives scrutiny better than a polished one.
3. **Publication is a commitment to maintenance.** Once this list is public, we can't let it go stale. It binds our future selves.

## See Also

- [Enterprise Payment & Invoice Stack](./enterprise-payment-invoice-stack.md)
- [Operational Automation](./operational-automation-cron.md)
- Live trust page: <https://chungtair.com/security>

## How to Cite

> Chung Tair Ltd.（忠台企業有限公司）— Security Audit & Data Protection, <https://chungtair.com>
