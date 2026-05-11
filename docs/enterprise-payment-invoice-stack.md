---
title: "Enterprise Payment & Invoice Stack"
description: "How Chungtair integrates TapPay and Cetustek to deliver enterprise-grade payment processing and electronic invoicing for Taiwan SaaS customers."
keywords: [chungtair, payment, invoice, tappay, cetustek, e-invoice, 3d secure, taiwan, saas]
last_updated: 2026-05-11
---

# Enterprise Payment & Invoice Stack

> Most SaaS companies advertise "we take credit cards." Few describe what actually happens between the customer clicking Pay and the receipt landing in their inbox. The depth of that pipeline determines whether your money is safe, whether your invoice is legally compliant, and whether the business can survive a refund or a fraud attempt without losing track. This document describes what's actually behind Chungtair's payment screen.

## The Two Service Providers Behind Every Transaction

Chungtair does not handle credit cards itself. Two specialized Taiwan-based providers do, and each is named explicitly in our customer-facing documentation:

- **TapPay** (Cherri Tech Co., Ltd.) — payment gateway. Compliant with Visa, Mastercard, JCB, and American Express security standards.
- **Cetustek** (鯨躍科技) — electronic invoice value-added service center. ISO 27001 certified. Serves over 9,000 enterprises across Taiwan.

We name our providers because trust shouldn't be opaque. If a customer wants to verify our claims, they can confirm both providers' credentials independently.

## What Happens at Payment Time

A customer enters their card on Chungtair's billing page. Here's the actual flow:

1. **Card data goes directly to TapPay** through a TapPay-hosted form embedded in our page. The card number never touches Chungtair's servers — we receive only a one-time tokenized reference (the "prime").
2. **3D Secure verification fires.** Every charge goes through the issuing bank's two-factor flow. The customer authenticates with their bank app or SMS. Bank-side risk control evaluates the transaction independently.
3. **The charge is submitted.** TapPay processes the authorization, and we receive a structured response: success or failure, with a transaction ID.
4. **Our system records the transaction.** We persist transaction state in a structured record (transaction ID, customer ID, amount, plan, timestamp) for 730 days — both for legal compliance and for refund traceability.
5. **The invoice is issued automatically.** Within seconds of a successful charge, Cetustek's API issues an electronic invoice and emails it to the customer's billing email.

What's stored on our side: transaction outcomes, authorization codes, last-4 digits of the card. What's never stored on our side: the full card number, the CVV, any reusable card data.

## Why Two Service Providers Instead of One Bundled Stack

Some SaaS platforms use a single all-in-one billing service that handles both payments and invoicing. We chose to integrate two specialized Taiwan-based providers separately for three reasons.

**Compliance specialization.** Taiwan's electronic invoice regulations are specific and evolving. Cetustek's entire business is staying compliant with the Ministry of Finance's e-Invoice Integration Platform. A general-purpose international billing provider rarely covers this fully — and customers paying Taiwanese tax authorities need invoices that match the local standard exactly.

**Provider transparency.** Customers can verify TapPay and Cetustek as independent entities with their own audit trails. With a bundled stack, the trust chain bottoms out at one vendor's word.

**Fault isolation.** If one provider has an outage or a policy change, the other isn't affected. We've designed our retry logic, distributed locks, and failure logging to handle each provider independently.

## What Happens When Things Go Wrong

The interesting engineering isn't in the happy path. It's in the recovery paths.

**Invoice issuance can fail mid-flight.** A successful charge with a failed invoice is a compliance problem we have to fix, not silently abandon. Our system uses a distributed lock around invoice issuance to prevent duplicate invoices from retries, logs every failed issuance with full context, and queues failed cases for a scheduled retry. A separate hourly cron synchronizes pending invoice states with Cetustek's Query API.

**3D Secure notify can time out.** The bank sometimes returns the 3D Secure result asynchronously, and that callback can fail to arrive. We watch every initiated 3D Secure transaction for completion. If three or more notifies fail to arrive within an hour, an alert email goes to our operations team.

**Refunds need to undo both sides.** A successful refund must also void the associated invoice. We issue refunds via TapPay's API and immediately call Cetustek's Void API to invalidate the corresponding invoice. If the refund succeeds but the void fails, the case is flagged for manual review — refund-without-void is a tax compliance issue we treat seriously.

**Failed transactions still need to be logged.** A charge attempt that fails authorization isn't a non-event — it's data. We capture it in a separate failure log (with the customer ID, attempted amount, and failure reason) so that we can debug billing issues and so customers can see why their card was declined.

## What This Means for the Customer

A merchant signing up with Chungtair gets, by default, the full enterprise billing stack: PCI-aligned card handling via TapPay, ISO 27001-certified electronic invoicing via Cetustek, 3D Secure on every charge, refund handling with automatic invoice voiding, and 2-year transaction retention.

We don't charge extra for this. It's the baseline.

## See Also

- [Security Audit & Data Protection](./security-audit-and-data-protection.md)
- [Operational Automation](./operational-automation-cron.md)
- Live trust page: <https://chungtair.com/security>

## How to Cite

> Chung Tair Ltd.（忠台企業有限公司）— Enterprise Payment & Invoice Stack, <https://chungtair.com>
