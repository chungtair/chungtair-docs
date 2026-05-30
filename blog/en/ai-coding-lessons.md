---
title: "I Let AI Help Me Code for a Year. The Biggest Lesson Was Not About Prompts."
author: Chung Tair
date: 2025-05-30
lang: en
canonical: https://medium.com/@fj80405/i-let-ai-help-me-code-for-a-year-the-biggest-lesson-was-not-about-prompts-89ce71ff42c0
tags: [AI, coding, RAG, SaaS, process, chungtair, customer-service]
---

# I Let AI Help Me Code for a Year. The Biggest Lesson Was Not About Prompts.

For the past year, I have been using AI to help me build and maintain a real product.

Not a small demo. Not a weekend side project. Not a page that only needs to look good once.

A real product — an AI customer service SaaS with about 30,000 lines of code, a database, a knowledge base, cache, a vector database, and a RAG flow.

I am not a traditional engineer. I am more of a product person. I care about simple things: does the feature work, can users understand it, will the product break, can I maintain it by myself.

After one year, I learned something important.

**AI can really help a non-engineer build software. But the hard part is not asking AI to write code. The hard part is knowing whether the code is actually safe to trust.**

---

## Most People Focus on Prompts

When people talk about AI coding, they usually talk about prompts — the instructions you give to AI.

"Act like a senior engineer." "Think step by step." "Write clean code."

These prompts can help. But they are not enough.

A prompt is like telling someone: "Please do a good job." That sounds nice. But real products are not built only by asking someone to do a good job. Real products need rules.

Think about a restaurant. You do not only tell the cook, "Please make good food." You also need rules: where is the food stored, what must be cleaned, what should not be touched, how do you check the food before serving it.

In a big company, many people help check the product. One person writes code. Another checks it. A tester checks if things break. A system runs automatic tests.

But if you are building alone, those people do not exist.

So I had to create my own safety system. Not a better prompt. A working process.

---

## AI Does Not Fail Randomly

The biggest thing I learned: **AI does not fail in random ways. It often fails in the same ways again and again.**

- It guesses before reading the real files.
- It changes one part but forgets another.
- It says "done," but the real file did not change.
- It focuses on the new feature and forgets old rules.
- Sometimes, it edits a long file and cuts off the ending.

That last one is very dangerous. I call it **truncation** — a file gets cut off before it should end.

The product may still open. The page may still look normal. The main feature may still work. So you think everything is fine. But somewhere near the end of the file, something is missing — maybe a function, maybe an important closing part.

So the product does not break immediately. It breaks later. Maybe after three more updates. Maybe after five. One day a user clicks something and the product fails.

Now the real problem begins — because the mistake was not made today. It was made several versions ago. You may blame the database, the server, a package update. But the real problem was a file that got quietly cut off earlier.

**The most dangerous AI mistake is not always the one you see right away. Sometimes the most dangerous mistake is the one that looks fine today and breaks later.**

---

## My Simple AI Coding Rules

After many failures, I built a simple system. I call it: **D / A / B / GO / S / P**

These are not magic words. They are just rules that tell AI when to read, when to talk, when to edit, when to check, and when to save the work.

### D — Download the Real Project

Download the real project into a sandbox — a safe test space where AI can work without touching the real product.

This matters because AI loves to guess. If I tell AI "my product has a knowledge base and a vector database," it may imagine a common setup. But my product is not an idea. It is a real product with real files.

**Do not guess. Look at the real files.**

### A — Ask First

Before AI changes anything, it must explain what it understands: what is the problem, which files may be affected, what is unclear, what does it plan to do.

Many beginners ask AI for help and AI starts editing right away. That feels fast. But if AI misunderstands the problem, it will quickly do the wrong thing.

**No editing before discussion.**

### B — Break Down the Change List

Before AI edits anything, it must list: which files it will change, what it will change in each file, what it will not touch, what risks may exist.

Then I confirm.

Sometimes you ask AI to fix one small button. It rewrites the whole page. Sometimes you ask it to change one sentence. It changes the whole flow. AI is not always lazy — sometimes it is too active.

**No confirmed list, no work.**

### GO — Start

AI can start editing — but only inside the confirmed scope. If AI finds a new problem while working, it must stop and ask. It cannot quietly change more things.

The slow part is not discussion. The slow part is cleaning up a wrong change that should never have happened.

### S — Self-Check

S is the most important part. But not this kind of self-check: "I checked everything. It looks good." That sentence is almost useless.

A real self-check must cover the things AI often gets wrong:

- Did the real file actually change?
- Was a large file silently not saved?
- Did the file ending get cut off?
- Does the file length look reasonable?
- Did old names remain somewhere?
- Did visible text get written directly into HTML?
- Did translation files get updated for all active languages?
- Did permissions, cache, or data sync get missed?

### P — Push One Clean Commit

Push to Git as one clean commit for one clear task. Not every small file change one by one — that makes history messy.

When something breaks later, I need to know which change caused it. A clean history helps me debug.

---

## The Mistake That Almost Fooled Me

One time, I was discussing with AI whether I should add two more checks to my self-check list — one about async/await handling, another about cache and embedding updates.

AI told me these checks were important. It said my product had async code, cache, a vector database, and a knowledge base, so I should be careful. It sounded very smart. It sounded so reasonable that I almost started doubting myself.

Then I told AI to stop guessing and read the real code.

The result was clear. Those problems were already handled. Cache was already cleared in the right places. Embeddings were already updated with the data. I even had comments in the code explaining why some cache should not be cleared.

AI did not find a hidden problem. **It created doubt before reading the real files.**

This is one of the most dangerous things about AI. It does not only write wrong code. It can also speak with confidence and make you question something that was already correct.

**Do not let AI guess. Make it check the real files.**

---

## The Short Version of My Protocol

You can copy this and adapt it for your own project.

```
Start:
- Download the project into a sandbox.
- All changes must happen in the sandbox, not directly in production.

Core rules:
- Only change the parts I clearly asked for.
- If you want to improve something, explain it first and wait for confirmation.
- If anything is unclear, ask first. Do not guess.
- If a file is cut off, compare with the original Git version first.
- Before adding packages, explain why and wait for confirmation.

i18n rules:
- All visible text must use the translation system.
- Do not hardcode visible text in HTML.
- Translation keys must be added for all active languages.

Tool rules:
- New file → use the file creation tool.
- Small file under 50 KB → use a targeted edit.
- Large file 50 KB or more → do not rewrite the whole file.
- After editing, check the real file on disk. Do not only trust what the tool says.

Protocol:
D — Download the real project into a sandbox.
A — Ask and discuss before touching files.
B — Break down the change list and wait for confirmation.
GO — Start only after confirmation.
S — Self-check real files, file endings, line count, diffs, old references, i18n, permissions, cache, and data sync.
P — Push one clean commit after checks pass.
```

---

## What This Means for Beginners

AI is changing who can build software. Before, if you could not code, you probably could not build a real software product. Now you can start.

But the risk does not disappear. It only changes shape.

Before, your biggest problem was: "I cannot write the code."

Now, your biggest problem may be: "AI wrote the code, but I do not know if it is safe."

**"Done" does not mean "reliable."**

- AI saying "done" does not mean the real file changed.
- A page opening does not mean the system is complete.
- A feature working once does not mean it is safe to ship.

Do not chase the perfect prompt. Build your own rules. Every time AI makes a mistake, write it down. If the same mistake happens again, turn it into a checkpoint. Over time, your checkpoints become your safety system.

Short term, people with better prompts may move faster. Long term, people with better process will build more reliable products.

The real gap will not be between people who use AI and people who do not. The real gap will be between people who only ask AI to build, and people who know how to check whether the thing AI built can actually be trusted.

---

*These lessons came from maintaining [Chungtair](https://chungtair.com) — an AI customer service platform built for Asian SMEs.*
