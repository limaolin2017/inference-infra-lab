# Mistakes — weak-point tracker

**Purpose:** count **what** was wrong and **how many times**, so weak points show up. Updated at every review (double backup: this file + project memory).
**Started:** 2026-11-23. Counts before 11-23 are rebuilt from [REVIEW_QUEUE.md](REVIEW_QUEUE.md), [reviews/archive](reviews/archive-2026-11-04-05.md) and [journal/](journal/), so they are a floor (may be a little low).

**What counts:** one **Miss** or **Partial** on a question = 1. A right answer does not reset the count; it updates *Last right*.

## Weak points by pattern

| Pattern | Wrong (total) | Worst items | What it means |
| --- | --- | --- | --- |
| **B. Direction / cause → effect** ("raise or lower?", "need vs fix") | **12** | `max_num_batched_tokens` 4×, WAITING gate 4× | Knows the parts, flips which way a knob moves the result |
| **A. Roles: who owns / does what** | **14** | evict meaning 3×, preempt unit 2×, request field vs block field 2× | Mixes up which component or field does the job |
| **C. Formula details** | 7 | offset = remainder 2× | Small steps inside a rule (now: assistant does arithmetic) |
| **F. GPU / memory concepts** | 8 | batching shares weights, not KV 2× | First-time misconceptions, mostly fixed |
| **D. Names and front / end** | 6 | `add` / `prepend` / `append_n` / `prepend_n` 3×, free queue front / end 2× | Right idea, wrong method name or side |
| **E. Next-day recall** | 2 | 3 Scheduler parts | Learned, then forgot after a day |

**Top weak points:** **B** and **A**. Review questions should test *direction* ("which way, and why?") and *roles* ("who decides / who does it?") first.

## Item log

| Area | Item | Pattern | Wrong | Dates wrong | Last right |
| --- | --- | --- | --- | --- | --- |
| Scheduler | `max_num_batched_tokens` direction (TTFT vs TPOT) | B | 4 | 11-10, 11-11, 11-13*, 11-16 | — |
| Scheduler | WAITING gate after preemption | B | 4 | 11-05?, 11-09, 11-10, 11-11 | 11-13 (guided) |
| Scheduler | `max_num_seqs` vs running requests | B | 1 | 11-09 | — |
| Scheduler | What triggers preemption | B | 1 | 11-23 | 11-23 (restated) |
| Scheduler | Preemption unit = request, not block | A | 2 | 11-05, 11-09 | — |
| Scheduler | Preempt: request field vs block field / what is reset | A | 2 | 11-09, 11-11 | 11-13 (guided) |
| Scheduler | Scheduler vs `kv_cache_manager` roles | A | 1 | 11-23 | — |
| Scheduler | 3 key parts (next-day recall) | E | 1 | 11-20 | 11-23 (guided) |
| Scheduler | `add_request` vs `prepend_request` | D | 2 | 11-19, 11-23 | — |
| BlockPool | Evict: what it removes, when | A | 3 | 11-09, 11-13, 11-16 | — |
| BlockPool | Shared block `ref_cnt` | A | 1 | 11-13 | 11-16 |
| BlockPool | `ref_cnt` 0 → back to free queue | A | 1 | 11-09 | 11-11 |
| BlockPool | Free queue front vs end (and why) | D | 2 | 11-11, 11-16 | 11-13 |
| BlockPool | `append_n` vs `prepend_n` | D | 1 | 11-13 | — |
| BlockPool | Which line takes blocks (`popleft_n`) | D | 1 | 11-13 | — |
| Model Runner | offset = remainder | C | 2 | 11-17 (×2) | — |
| Model Runner | Use the physical block | C | 1 | 11-17 | — |
| Model Runner | pos counts from 0 | C | 1 | 11-17 | — |
| Model Runner | Decode: `slot_mapping` size = 1 per request | C | 1 | 11-17 | — |
| Model Runner | `slot_mapping` vs `block_table` | A | 1 | 11-17 | — |
| Model Runner | `input_ids` vs `positions` | A | 1 | 11-10 | 11-13 |
| Model Runner | Model Runner does not allocate blocks | A | 1 | 11-09 | — |
| Model Runner | Chunked prefill: how chunks combine | F | 1 | 11-10 | — |
| GPU | Memory-bound data path (HBM → SRAM, not CPU → GPU) | F | 1 | 11-10 | — |
| GPU | Batching shares weights, not KV (why big batch → slower TPOT) | F | 2 | 11-10, 11-23 | — |
| GPU | Decode reads weights too | F | 1 | 11-10 | — |
| GPU | Throughput vs per-user TPOT | B | 1 | 11-10 | — |
| KV memory | MHA / GQA / MQA = KV head count | F | 1 | 11-17 | — |
| KV memory | Why vLLM reserves KV at startup | F | 1 | 11-17 | — |
| KV memory | Leading 2 (K and V) | C | 1 | 11-23 | — |
| KV memory | Bits vs bytes (FP16 = 2) | C | 1 | 11-23 | — |
| KV memory | "Which concept is this?" (formula not recalled) | E | 1 | 11-23 | — |
| KV memory | 4× users: need (× 4) vs fix (smaller per token) | B | 1 | 11-23 | — |
| KV memory | Quantization: only weights, missed KV cache | A | 1 | 11-23 | 11-23 (same day) |
| GPU | Why K/V is read per user: mixed prefix-cache hit with decode reads | A | 1 | 11-23 | — |

\* 11-13: not reviewed, counted in the source as a forgotten check. `?` = from the archive, date not exact.

## How to update (each review)

1. For every Miss / Partial: +1 on its row (or add a row), add the date.
2. For a right answer: write the date in *Last right* (no reset).
3. Recount the pattern totals. Copy the top 2 patterns into project memory.
