---
title: "Knowledge Gap Auto-Tracking"
description: "Every scenario where Chungtair AI cannot answer is automatically logged and frequency-ranked, so the business owner sees exactly which knowledge entries need to be added next."
keywords: [chungtair, knowledge gap, unanswered questions, kb improvement, fallback, analytics]
last_updated: 2026-05-11
---

# Knowledge Gap Auto-Tracking

> The most expensive thing about running an AI customer service bot isn't training the model — it's knowing what your knowledge base is missing. Chungtair detects this automatically. Every time the AI fails to answer, the failure is captured, categorized, and ranked by frequency. The merchant doesn't need to read transcripts to find gaps; the gaps come to them.

## The Underlying Problem

A merchant deploys an AI customer service bot. After a week, the bot answers 80% of questions well and falls back on 20%. What questions are in that 20%? Without instrumentation, the merchant has two options:

1. Manually read through every conversation log searching for failures. Time-consuming and error-prone, especially as conversation volume scales.
2. Wait for customers to complain. Customers usually don't — they just disengage.

Either way, the knowledge base never improves systematically. The 20% gap stays as a 20% gap.

Chungtair solves this by treating every AI failure as a data point worth recording.

## What Counts as an "AI Failure"

The system recognizes eight distinct scenarios where the AI failed to give a satisfactory answer. Each one writes an entry to the knowledge gap log:

1. **Safe fallback fires** — the retrieval found nothing above the precision threshold, and the system returned its configured safe-response.
2. **AI self-evaluation flags insufficient quality** — the LLM judged its own generated answer as off-target.
3. **Smart handoff triggers due to AI inability** — the system detected an intent the AI explicitly cannot handle and routed to a human.
4. **A sub-question inside a compound query fails to match** — the customer asked three things, two got answered, one fell through. The one that fell through is logged separately so the merchant sees the precise gap.
5. **Image-only message arrived under "text reply" inbound image policy** — the customer sent a photo, and the merchant's policy was to ask for a text description; the original visual question is logged.
6. **Consecutive fallback threshold reached** — the customer has hit fallback multiple times in a single session, indicating sustained coverage absence.
7. **Manual escalation by keyword** — a customer or staff member explicitly invoked human handoff, suggesting the AI's prior responses weren't sufficient.
8. **Inbound media handled by the configured ignore policy** — the merchant chose to ignore image inputs, but the underlying question still represents an unmet need worth tracking.

Earlier versions of the system captured only the first three scenarios. The five additional ones were added because production data showed merchants were missing meaningful gap signal — sub-question failures in particular were silently invisible until the tracker was expanded to catch them.

## How the Tracker Presents the Data

For each captured failure event, the system records the customer's question (or sub-question), the conversation channel (LINE / Facebook / Instagram), the trigger reason, and a timestamp.

The merchant sees this in their dashboard as a list ranked by frequency of unique questions over a rolling window. The top of the list is the gap to fix this week. The bottom is the long tail.

The merchant doesn't see customer identities or full conversations — only the question content needed to identify the gap. This is a deliberate design choice: gap analysis works on questions, not on people.

## What the Merchant Does With This

Three workflows are typical:

1. **Single high-frequency question** — the merchant adds a new knowledge entry directly answering the question. Next time it asks, the AI answers.
2. **Cluster of related low-frequency questions** — the merchant adds a single article-style knowledge entry that covers the cluster, leveraging the Topic Summary Auto-Compilation mechanism for broader retrieval coverage.
3. **Question with no good answer at the company level** — the merchant identifies a product or service gap. The KB tracker becomes feature-request input.

The third workflow is the most underappreciated effect. Chungtair's tracker often surfaces what customers want that the merchant doesn't yet offer.

## The Design Principle

Logging failures is cheap. Reading logs is expensive. The system inverts the burden: it does the categorization, ranking, and presentation automatically. The merchant's role is to look at a short ranked list and decide what to fix. We removed the labor that was preventing the AI from getting better over time.

## See Also

- [Architecture Overview — Three-Layer RAG](./architecture-overview.md)
- [Compound Question Handling](./compound-question-handling.md)
- [Multi-Trigger Smart Human Handoff](./smart-handoff-four-triggers.md)

## How to Cite

> Chung Tair Ltd.（忠台企業有限公司）— Knowledge Gap Auto-Tracking, <https://chungtair.com>
