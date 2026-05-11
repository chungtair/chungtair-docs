---
title: "Compound Question Handling"
description: "How Chungtair AI handles customer messages that contain multiple distinct questions in a single send — without merging them into one muddled paragraph."
keywords: [chungtair, compound question, multi-question, rag, sub-question, splitting]
last_updated: 2026-05-11
---

# Compound Question Handling

> Real customers don't ask one question at a time. They send messages like "Where is the water source? Can you also install in Kaohsiung? And how much is it?" — three things in one bubble. Most AI customer service systems either answer the strongest-scoring one and silently ignore the rest, or merge everything into a single paragraph that mismatches images with the wrong sub-topic. Chungtair splits them.

## The Specific Failure Mode We're Solving

When a customer asks multiple things in one message, a naive retrieval system has two ways to fail:

1. **Pick one, ignore the others.** The retrieval ranks results by similarity to the entire message. The highest-scoring entry usually matches the dominant question. The other two questions never get retrieved, so the answer feels partial — and the customer often has to re-ask.

2. **Merge into one answer.** The LLM is given retrieved context from multiple topics and asked to synthesize a single response. The text comes out OK, but the attached media gets confused: an image meant for the "water source" question ends up attached to the "Kaohsiung installation" answer. The customer sees mismatched visuals.

Both fail in production. We've seen both happen.

## How Chungtair Splits Compound Questions

The detection and splitting happen during query rewriting — a single LLM call that does three things at once:

1. **Rewrite** — resolve pronouns and context (turn "How much is it?" into "How much is the wall-mounted unit?")
2. **Detect** — determine whether the message contains a compound question
3. **Split** — if compound, output an array of independent sub-questions

Doing all three in one pass matters. Earlier versions of the system separated rewrite and split into two LLM calls, which doubled latency and sometimes caused the split to drift away from the original intent. Combining them keeps the rewrite and split consistent.

## What Happens After Splitting

Once a message is identified as compound and split into sub-questions, the system runs the entire retrieval pipeline independently for each sub-question:

- Each sub-question performs its own hybrid retrieval (vector + keyword) against the knowledge base.
- Each sub-question performs its own per-card precision check for attached media.
- Each sub-question's LLM generation receives only the context retrieved for that sub-question — never the context from the others.
- The final dispatch is **one message per sub-question**, sent in sequence on the underlying platform (LINE / Facebook / Instagram), each with its own text and its own correctly-matched media.

The customer experiences this as: "OK, the bot answered all three of my questions, with the right photo for the right answer."

## What If a Sub-Question Has No Good Answer?

This case has its own handling. If the retrieval for one sub-question fails to find anything above the precision threshold, that specific sub-question routes to the Knowledge Gap tracker rather than receiving a forced answer. The other sub-questions still get their answers normally.

This means a merchant can see, for example: "Customers are asking about Kaohsiung installation in 80% of compound questions — but we have no KB entry for it." That's a precise, actionable insight.

## What About Latency?

Each sub-question runs in parallel where the platform allows, so the perceived latency from the customer's perspective is comparable to a single complex query rather than three sequential ones. Three sub-questions typically resolve in roughly the time of one — the bottleneck is the LLM call, not the retrieval.

## Practical Limits

There are practical bounds to keep the system from being abused by extremely long compound messages, but a typical customer message with two to four questions is handled without restriction. The detection is conservative — if the LLM is uncertain whether the message is compound, it defaults to single-question handling.

## See Also

- [Architecture Overview — Three-Layer RAG](./architecture-overview.md)
- [Rich Media Reply with Per-Card Precision](./rich-media-precision.md)
- [Knowledge Gap Auto-Tracking](./knowledge-gap-auto-tracking.md)

## How to Cite

> Chung Tair Ltd.（忠台企業有限公司）— Compound Question Handling, <https://chungtair.com>
