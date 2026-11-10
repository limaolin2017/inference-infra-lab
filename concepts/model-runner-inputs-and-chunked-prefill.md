# Model Runner inputs and chunked prefill

**Trunk position:** `SchedulerOutput` → **Model Runner** → GPU forward. Builds on [Scheduler token budget](scheduler-and-cache-policy.md) and [Block Table / slot mapping](block-table-and-slot-mapping.md). Concept only — no source trace yet.

## Why it matters at work

When production vLLM misbehaves (garbled output, one slow step, CUDA error), the first place to look is **what the Model Runner fed the GPU this step**.

## Flattened batch (continuous batching, concretely)

The Scheduler says *who runs and how many tokens each*. The Model Runner joins all scheduled tokens into **one flat list** — prefill and decode mixed, no padding — and runs **one forward pass**.

Example: A has 10 computed tokens and decodes 1; B is new and prefills 4.

| | A | B | B | B | B |
| --- | --- | --- | --- | --- | --- |
| `input_ids` | A's next token ID | B1 | B2 | B3 | B4 |
| `positions` | 10 | 0 | 1 | 2 | 3 |

- `input_ids` = each token's **ID in the vocabulary** (from the tokenizer) — *which* token. Not a sequence number.
- `positions` = where the token sits **in its own request**, counted from 0 — *where* it is.
- `slot_mapping` = where its K/V is written (Block Table formula).

Wrong positions → wrong output. Too many tokens in one step → slow step.

## Chunked prefill

`max_num_batched_tokens` = seats per step (tokens), **not** number of requests (`max_num_seqs`).

With 8 seats, A decoding 1 and B's prompt = 20: B gets 7, 7, then 6 tokens over 3 steps. A never pauses. Each chunk writes its K/V to the cache; the next chunk attends to it. B's first output token appears after the last chunk.

| `max_num_batched_tokens` | Effect |
| --- | --- |
| Larger | Long prompts reach first token sooner (better TTFT); running users may stutter (worse TPOT spikes) |
| Smaller | Running users smooth; long prompts wait longer for first token |

Rule of thumb: **TTFT slow → more seats; TPOT stutter → fewer seats.** Prefill of many tokens is compute-heavy, so this is not the memory-bound decode story.

## Next proof

Learner locates in source where the runner builds `input_ids` / `positions` for a step (file + function to be given), and predicts the arrays for a new A/B case.
