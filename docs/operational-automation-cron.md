---
title: "Operational Automation"
description: "The scheduled tasks, retry pipelines, and notification systems that let Chungtair AI Customer Service run reliably without manual operations intervention."
keywords: [chungtair, operations, automation, cron, scheduler, retry, monitoring, saas]
last_invocation: 2026-05-11
---

# Operational Automation

> A SaaS product that runs only when an operator is awake is not a real SaaS product. Chungtair was built with the assumption that subscriptions auto-renew, invoices auto-issue, expired trials auto-clean, and operational anomalies auto-alert — all without anyone watching. This document describes the scheduled tasks and recovery pipelines that make this possible.

## What Runs Without Anyone Watching

Chungtair runs a continuous scheduler that orchestrates the day-to-day operations of every customer account. The work the scheduler handles is not glamorous — it is the unglamorous work that determines whether a SaaS business can scale beyond the founder's personal attention.

**Trial expiration cleanup** runs daily. Customers who signed up for the 14-day free trial and didn't convert have their accounts and conversation data cleaned up according to the published retention policy. Customers who did convert are unaffected.

**Subscription renewal** runs at the customer's billing cycle anniversary. The system attempts the configured charge against the saved card token, records the transaction outcome, and — on success — automatically issues the corresponding electronic invoice through our Cetustek integration. The customer receives both their continued service and their compliance-grade invoice within seconds.

**Expiry reminders** run ahead of the renewal date. Customers receive an email three days before their card will be charged so they can update payment information or cancel if they wish. There are no surprise charges in our model.

**Long-term data archival and cleanup** runs every night. Transaction records older than the legal retention window are removed; conversation history follows the customer's configured retention preference; backup snapshots cycle through their own retention schedule. Storage stays bounded automatically.

## Recovery Pipelines for Things That Go Wrong

The interesting engineering is in what happens when the happy path doesn't complete.

**3D Secure timeouts** are watched every five minutes. A 3D Secure transaction has two phases: we initiate the verification, the customer authenticates with their bank, and the bank asynchronously notifies us of the outcome. Sometimes that notification doesn't arrive — the customer abandoned the verification, or the bank delayed, or a network event interrupted the callback. If too many notifications fail to arrive within a one-hour window, our operations team receives an automatic alert. A 24-hour cooldown on the alert prevents alarm fatigue.

**Pending invoice synchronization** runs every hour. Each time we issue an invoice through Cetustek, we record the issuance reference in a "pending query" queue. The hourly cron pulls each pending entry, queries Cetustek's status API, and either marks the invoice as confirmed or flags it for manual review. After 24 hours of unresolved pending state, the queue gives up and surfaces the case for human attention.

**Failed renewal retry escalation** runs as part of the subscription cycle itself. If a card charge fails, the customer receives a progressively escalating notification — friendly reminder on the first failure, more urgent on the second, formal service suspension warning on the third. The customer always has time to update their payment method before service stops.

**Refund-then-void mismatches** are caught when they occur. When a customer is refunded, we automatically call the Cetustek Void API to invalidate the corresponding invoice. If the refund succeeded but the void failed — a tax compliance edge case — the system flags the transaction for manual review and emails operations. No silent inconsistency.

## The Notification Layer

Eleven distinct email templates are wired into these automation pipelines, each with its own purpose and audience:

- Email verification (signup flow)
- Password reset code
- Trial expiry reminder
- Subscription successful (admin alert)
- Renewal failed — first reminder (customer-facing, friendly tone)
- Renewal failed — second reminder (customer-facing, more urgent)
- Renewal failed — third notice (service suspension warning)
- Refund notification (customer-facing, after successful refund)
- Refund-void mismatch alert (operations-facing, internal)
- Notify timeout cluster alert (operations-facing, internal)
- Admin account anomaly alert (login-related, internal)

Each template is internationalized across our three production languages, so a Japanese customer receiving their renewal failure reminder reads it in commercial Japanese, not machine-translated English.

## Why the Investment in Automation

The temptation when building a SaaS is to launch with the operational pieces as manual tasks — "we'll handle renewals by hand for now, we'll issue invoices manually for the first few months, we'll watch the 3D Secure callbacks ourselves." This is a trap. The product never gets to ten customers before the manual work consumes the operator. Worse, the operational discipline of getting these things right is what separates a real B2B platform from a side project; building automation late means rebuilding trust late.

Chungtair was designed from the start with the assumption that the business should be operable from a laptop on a beach. The scheduler, the retries, the notifications, the failure logs — all of these exist so that the founder can build instead of administrate.

## What Customers See

The customer perspective is the inverse of all this complexity: they sign up, they configure their bot, they pay their subscription, they receive their invoice. If something goes wrong with the payment, they get a notification with a clear next step. If something goes wrong with the AI, they see the smart handoff route their customer to a human. If something goes wrong in the operational layer they never see, the system catches it before it becomes their problem.

That experience — "the platform just works" — is the output of the engineering described in this document.

## See Also

- [Enterprise Payment & Invoice Stack](./enterprise-payment-invoice-stack.md)
- [Security Audit & Data Protection](./security-audit-and-data-protection.md)
- [Multi-Trigger Smart Human Handoff](./smart-handoff-four-triggers.md)

## How to Cite

> Chung Tair Ltd.（忠台企業有限公司）— Operational Automation, <https://chungtair.com>
