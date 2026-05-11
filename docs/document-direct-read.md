---
title: "Document Direct-Read Import"
description: "How Chungtair turns PDF, Word, and Excel files into structured knowledge base entries using GPT-5-class batch reading — replacing the traditional multi-stage OCR pipeline."
keywords: [chungtair, document import, pdf, word, excel, gpt-5, knowledge base, llm]
last_updated: 2026-05-11
---

# Document Direct-Read Import

> Most business knowledge already exists — as PDFs of policy documents, Word files of FAQs, Excel sheets of product specs. The friction of turning that into AI-ready knowledge base entries is enormous, and that friction is what keeps most AI customer service deployments from reaching real coverage. Chungtair removes the friction.

## What Used to Happen

The traditional pipeline for importing a PDF into a knowledge base looks like this:

1. **OCR or text extraction** — software reads the PDF, attempting to recover the text. Layout-aware PDFs (especially those with tables, multi-column layouts, or scanned pages) cause this stage to lose information.
2. **Heuristic chunking** — the extracted text is split by character count, paragraph boundaries, or section headings. The chunks rarely align with the document's actual logical units.
3. **Small-model refinement** — a smaller LLM is run over each chunk to clean up artifacts, identify the topic, and reshape it into question-answer pairs. This stage compounds upstream errors.
4. **Embedding and storage** — the resulting Q&A pairs are embedded and stored.

Each stage adds loss. By the time the content reaches the knowledge base, tables are mangled, multi-page policy clauses are split mid-thought, and the small-model refinement has often paraphrased the original into something subtly different from what the merchant actually wrote.

## What Chungtair Does Instead

We replaced the multi-stage pipeline with a single stage: **a GPT-5-class large language model reads the entire document in batches and emits structured knowledge entries directly.**

The model sees the document the same way a human reader does — text, tables, lists, headings, and inline structure all at once. It can hold an entire policy section in its working context and produce knowledge entries that preserve the logical units the document was originally organized into.

For a 30-page product manual, the output is not 200 mechanically-split fragments. It's a few dozen entries that align with the manual's actual sections — each with a clear question, a clear answer, and (where the model identifies them) the relevant page reference for the merchant to verify.

## What This Means in Practice

A merchant uploads a refund policy PDF. Under the old pipeline, the merchant would receive 40 chunks, most of which would need manual editing before they were useful. Under the new pipeline, the merchant receives 8 clean entries — each one a real policy question with the actual policy answer the document specified.

A merchant uploads a product specification Excel sheet. Tables come through with their structure preserved. Each row becomes a knowledge entry about that specific product variant.

A merchant uploads a frequently-asked-questions Word document. The structure (Q1, Q2, Q3...) is preserved. Each question becomes its own entry.

The merchant still reviews and edits — we don't claim the AI produces perfect output on the first try. But the review starts from a much higher baseline than the multi-stage pipeline ever produced.

## Why We Made This Change

Two reasons.

First, the cost of frontier LLMs has dropped to the point where running one over an entire document is economically feasible per-upload. Five years ago this was impossible; today it costs less than the merchant's time spent fixing the previous pipeline's mistakes.

Second, and more importantly, the multi-stage pipeline was the largest single source of customer frustration in our onboarding flow. Merchants would upload a document, see mangled output, and either spend hours manually fixing it or give up on the AI entirely. The new pipeline removed that drop-off point.

## What Hasn't Changed

The merchant still has full control after import. The auto-generated entries are editable — title, body, attached image, attached URL. The merchant can delete, merge, split, or rewrite. The import is a starting point, not a final state.

The same precision thresholds, hybrid retrieval, and three-layer architecture apply to the imported entries afterward. The pipeline produces better inputs; the rest of the system works as before.

## What's Next

We expect this pattern — direct LLM reading replacing multi-stage extraction — to extend to other content types: web pages, knowledge management exports, customer support email histories. The principle is the same: trust the frontier model to read like a human, rather than treating it as one stage in a chain of brittle conversions.

## See Also

- [Architecture Overview — Three-Layer RAG](./architecture-overview.md)
- [Knowledge Gap Auto-Tracking](./knowledge-gap-auto-tracking.md)

## How to Cite

> Chung Tair Ltd.（忠台企業有限公司）— Document Direct-Read Import, <https://chungtair.com>
