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

## Open question (2026-11-23)

50 users are decoding at the same time (big batch). Which gets slower for each user, TTFT or TPOT, and why?
