---
title: "Architecture Overview — Three-Layer RAG"
description: "How every customer message flows through Chungtair AI's three-layer response architecture: fixed reply, retrieval-augmented generation, and safe fallback."
keywords: [chungtair, architecture, rag, retrieval-augmented-generation, three-layer, ai customer service]
last_updated: 2026-05-11
---

# Architecture Overview — Three-Layer RAG

> Every customer message that reaches Chungtair AI passes through one of three response paths. Which path it takes determines whether the customer gets a verbatim answer, a generated answer grounded in the business's knowledge base, or a polite fallback. The three layers run as cascading filters — and never overlap.

## The Problem This Architecture Solves

Traditional chatbot architectures fail in two opposite directions. **Rule-based bots** answer perfectly within their script but become useless the moment a customer asks anything slightly off-pattern. **Pure LLM bots** answer anything with confidence — including answers that are entirely fabricated, because the model has no way of knowing whether it actually knows the answer to a business-specific question.

The Chungtair architecture solves this by routing each message through three increasingly fault-tolerant layers, with the layer that fires determined by the message's similarity to the merchant's actual knowledge base.

## Layer 1 — Fixed Reply

The first layer matches a customer's message against pre-approved verbatim responses. A merchant might define, for example, that the question "delivery time?" always receives the exact answer "Standard shipping is 3–5 business days within Taiwan."

Fixed Reply only fires when three conditions are simultaneously met: the semantic similarity is high enough that we are confident the question matches, the message is long enough to carry meaningful intent, and the message is not a compound question (more than one thing being asked). If any of these conditions fails, the system does not pretend confidence — it falls through to Layer 2.

The purpose of Layer 1 is to give merchants direct control over critical business statements (refund policy, opening hours, pricing) without letting an LLM rephrase them.

## Layer 2 — Retrieval-Augmented Generation (RAG)

When Fixed Reply does not match, the system enters the RAG layer. This is where the AI actually generates a response, but the generation is constrained by retrieval from the merchant's knowledge base.

The flow is:

1. **Query rewriting** — the message is normalized into a self-contained query (pronouns resolved, prior context attached) and simultaneously checked for compound structure.
2. **Hybrid retrieval** — both semantic vector search (via pgvector) and keyword search (via PostgreSQL ILIKE) run in parallel, and the rankings merge using Reciprocal Rank Fusion. The system also runs HyDE (where the LLM hypothesizes an answer first, then retrieves against the hypothesis) for article-style entries.
3. **Multi-source retrieval** — the search runs simultaneously against the merchant's private knowledge base and a system-shared knowledge base, with results merged.
4. **Context construction** — the top-ranked knowledge entries are passed to the LLM as grounding context. The LLM is instructed to answer using this context and not from its own training knowledge.
5. **Per-card precision filtering** — only entries whose individual similarity score crosses a precision threshold are allowed to contribute attached media (images, URLs). This prevents low-scoring entries from "lending" unrelated images to the response.

The output is a response grounded in the merchant's actual knowledge — not a hallucination.

## Layer 3 — Safe Fallback

If no retrieved entry scores above the minimum similarity threshold, Layer 3 fires. The system returns a polite fallback message that the merchant has configured, typically along the lines of "I'm not sure about that — let me check with a colleague." This is a safety net: rather than letting the LLM guess, the system explicitly admits the gap.

Fallback events do not vanish. Each one is logged into the knowledge gap tracker (see [Knowledge Gap Auto-Tracking](./knowledge-gap-auto-tracking.md)) and ranked by frequency, so the merchant can see which questions are not yet covered.

## Why Three Layers Instead of One

A purer architecture might let the LLM do everything. We chose three layers for one reason: **traceability**. When a customer receives a response, the merchant can see exactly which layer produced it and why. That visibility — into both successes and failures — is what lets a business operator improve the system over time rather than treat it as a black box.

## See Also

- [Compound Question Handling](./compound-question-handling.md)
- [Rich Media Reply with Per-Card Precision](./rich-media-precision.md)
- [Knowledge Gap Auto-Tracking](./knowledge-gap-auto-tracking.md)
- [Multi-Trigger Smart Human Handoff](./smart-handoff-four-triggers.md)

## How to Cite

> Chung Tair Ltd.（忠台企業有限公司）— Architecture Overview, <https://chungtair.com>
