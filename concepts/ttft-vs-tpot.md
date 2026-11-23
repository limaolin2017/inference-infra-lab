# TTFT vs TPOT — what limits each

**Status:** concept started 2026-11-23 (picture shown, first question open). Main line item 2. Colab gate check **B**.
**Main path:** Request → Scheduler → KV Cache Manager / BlockPool → Model Runner → **GPU** (prefill / decode steps)
**Component pages:** [scheduler](../components/scheduler.md) · [model-runner](../components/model-runner.md)

![TTFT vs TPOT](../assets/ttft-vs-tpot.svg)

## One sentence

TTFT (time to first token) is how long the user waits before the first word; TPOT (time per output token) is how fast the next words come. They are limited by different things.

## Why it matters at work

Most latency complaints are one of two: "it takes long to start" (TTFT) or "the text comes out slowly" (TPOT). You must know which knob fixes which — this is the learner's weak pattern **B (direction)** in [MISTAKES.md](../MISTAKES.md).

Restaurant: TTFT = wait for the first dish (the kitchen reads the whole order first); TPOT = time between dishes.

## So far

- TTFT = queue wait + **prefill** (whole prompt in one go, compute-bound).
- TPOT = one **decode** step per token (memory-bound — see [memory-bound-and-batching](memory-bound-and-batching.md)).

## Answered (2026-11-23)

**10,000-token prompt → TTFT gets much slower**, because prefill must process all 10,000 tokens before the first output token. → **Right · own reasoning, first try.**

Extra: TPOT also gets a bit slower — every decode step reads the K/V of all 10,000 earlier tokens (memory-bound), but much less than TTFT.

Word note: *prefill* = processing the whole prompt and writing its K/V, not "filling in" tokens.

**50 users decoding together → TPOT gets slower.** Learner: "TPOT, because this is the decode phase, not the first token" → **Partial**: right conclusion; the reason only says why it is *not* TTFT. Missing mechanism: every decode step must read the K/V of all 50 users (weights are read once and shared, K/V is per user), so each step takes longer → every user waits longer between tokens. See [memory-bound-and-batching](memory-bound-and-batching.md).

**Learner's question:** why are the weights read once for 50 users, but K/V 50 times? Learner's own guess: "weights are fixed; K/V depends on whether each user's prompt hits the cache" → **Partial**: *weights are the same for everyone* is right; the cache-hit part mixes up **prefix caching** (saves compute at prefill) with **decode reads**. Correct: each user's K/V is **different data** (their own tokens), so the GPU must read each user's K/V separately, every step — cache hit or not. Restaurant: one recipe book for all tables; but each table's order is different, so the chef reads each order.

## Open question (2026-11-23)

Batch goes from 1 user to 50 users. Per decode step, which read grows with the batch: the weights, or the K/V? Why does that make TPOT slower?
