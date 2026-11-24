# Mistakes — weak-point tracker

**Purpose:** count **what** was wrong and **how many times**, so weak points show up. Updated at every review (double backup: this file + project memory).
**Started:** 2026-11-23. Counts before 11-23 are rebuilt from [REVIEW_QUEUE.md](REVIEW_QUEUE.md), [reviews/archive](reviews/archive-2026-11-04-05.md) and [journal/](journal/), so they are a floor (may be a little low).

**What counts:** one **Miss** or **Partial** on a question = 1. A right answer does not reset the count; it updates *Last right*.
**Miss / Partial split (from 2026-11-24 part 2, learner's idea):** new dates in *Dates wrong* carry `(M)` = Miss or `(P)` = Partial. Older dates are not split. Tag **G** marks "right conclusion, reason missing" answers; G is a tag, so it is **not** added to the pattern totals.

## Weak points by pattern

**Sub-pattern seen 2× on 11-23:** right conclusion, but the reason explains something else ("decode, not first token"; "must wait for prefill"). Ask: *what changed, and why does that change the result?*


| Pattern | Wrong (total) | Since 11-24 pt 2: Miss / Partial | Worst items | What it means |
| --- | --- | --- | --- | --- |
| **B. Direction / cause → effect** ("raise or lower?", "need vs fix") | **20** | 0 / 0 | `max_num_batched_tokens` 5× (last right 11-24, no hint), WAITING gate 4×, side-by-side causes 3× | Knows the parts, flips which way a knob moves the result |
| **A. Roles: who owns / does what** | **21** | 2 / 2 | who builds / uses `slot_mapping` 2× (11-24), which knob owns tokens vs requests 1× (11-24), evict meaning 3×, preempt unit 2×, request field vs block field 2× | Mixes up which component or field does the job |
| **C. Formula details** | 8 | 0 / 0 | offset = remainder 2×, bits vs bytes 2× | Small steps inside a rule (now: assistant does arithmetic) |
| **F. GPU / memory concepts** | 14 | 0 / 2 | batching shares weights, not KV 3×, compute vs load 3× | First-time misconceptions, mostly fixed |
| **D. Names and front / end** | 7 | 0 / 0 | `add` / `prepend` / `append_n` / `prepend_n` 3×, free queue front / end 2× | Right idea, wrong method name or side |
| **E. Next-day recall** | 2 | 0 / 0 | 3 Scheduler parts | Learned, then forgot after a day |
| *G. Reason missing (tag)* | *5* | *0 / 1* | many users → TPOT mechanism 2× (11-23, 11-24); chunked → TTFT 11-23; knob TPOT cost 11-23 eve; "more compute for decode" 11-24 | Right conclusion, but the reason is missing or explains something else. Ask: *what changed, and why does that change the result?* |

**Top weak points:** **B** and **A** (recounted 11-24 part 2). Watch tag **G**. Review questions should test *direction* ("which way, and why?") and *roles* ("who decides / who does it?") first.

## Item log

| Area | Item | Pattern | Wrong | Dates wrong | Last right |
| --- | --- | --- | --- | --- | --- |
| Scheduler | `max_num_batched_tokens` direction (TTFT vs TPOT) — also thought it is a memory setting | B | 5 | 11-10, 11-11, 11-13*, 11-16, 11-23 | **11-24 (reverse case, first try, no hint)** |
| Scheduler | WAITING gate after preemption | B | 4 | 11-05?, 11-09, 11-10, 11-11 | 11-13 (guided) |
| Scheduler | `max_num_seqs` vs running requests | B | 1 | 11-09 | 11-24 (E waits, first try) |
| Scheduler | Which knob: tokens per step (`max_num_batched_tokens`) vs requests per step (`max_num_seqs`) — long doc is 1 request | A | 1 | 11-24 (M) | 11-24 (restated, guided) |
| Scheduler | What triggers preemption | B | 1 | 11-23 | 11-24 (new request never preempts, first try) |
| Scheduler | Preemption unit = request, not block | A | 2 | 11-05, 11-09 | — |
| Scheduler | Preempt: request field vs block field / what is reset (11-24: said blocks stay) | A | 3 | 11-09, 11-11, 11-24 | 11-24 (restated) |
| Scheduler | Scheduler vs `kv_cache_manager` roles | A | 1 | 11-23 | 11-24 (first try) |
| Scheduler | 3 key parts (next-day recall) | E | 1 | 11-20 | 11-23 (guided) |
| Scheduler | `add_request` vs `prepend_request` | D | 2 | 11-19, 11-23 | — |
| Scheduler | Preempted → **front** of `waiting` (said only "waiting") | D | 1 | 11-24 | 11-24 (restated) |
| Scheduler | Why lowering the knob helps (said "more compute for decode"; correct: shorter step) | B | 1 | 11-24 | 11-24 (restated) |
| Scheduler | Side-by-side causes: knob cost vs own K/V growth (linked as cause → effect) | B | 3 | 11-23 eve (×2), 11-24 | 11-24 (restated) |
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
| Model Runner | `slot_mapping` vs `block_table` (11-24: described it as logical → physical block) | A | 2 | 11-17, 11-24 (P) | 11-24 read vs write (first try) |
| Model Runner | Who builds / uses `slot_mapping` (said KV Cache Manager; then "Model Runner uses") | A | 2 | 11-24 (M), 11-24 (P) | — |
| Model Runner | `input_ids` vs `positions` | A | 1 | 11-10 | 11-13 |
| Model Runner | Model Runner does not allocate blocks | A | 1 | 11-09 | — |
| Model Runner | Chunked prefill: how chunks combine; that chunks share a step with decode tokens; first token at the last chunk | F | 3 | 11-10, 11-23, 11-24 | 11-24 (one step = chunk + others' decode, right) |
| GPU | Prefill **computes** K/V, decode **reads** it (said "load" / 读取) | F | 3 | 11-23 eve, 11-24, 11-24 pt 2 (P) | 11-24 (asked why → right) |
| KV memory | Why weight quantization fits more users (bigger KV budget) | F | 1 | 11-24 | 11-24 (guided) |
| GPU | Memory-bound data path (HBM → SRAM, not CPU → GPU) | F | 1 | 11-10 | — |
| GPU | Batching shares weights, not KV (why big batch → slower TPOT) — tag G on 11-24 | F | 3 | 11-10, 11-23, 11-24 pt 2 (P) | 11-24 pt 2 (own words, after explanation) |
| GPU | Decode reads weights too | F | 1 | 11-10 | — |
| GPU | Throughput vs per-user TPOT | B | 1 | 11-10 | — |
| GPU | TTFT = compute-bound, TPOT = memory-bound (swapped; forgot "compute-bound") | B | 2 | 11-23, 11-24 | 11-24 (restated) |
| KV memory | MHA / GQA / MQA = KV head count | F | 1 | 11-17 | — |
| KV memory | Why vLLM reserves KV at startup | F | 1 | 11-17 | — |
| KV memory | Leading 2 (K and V) | C | 1 | 11-23 | 11-24 (first try) |
| KV memory | Bits vs bytes (FP16 = 2) | C | 2 | 11-23, 11-24 | 11-24 (restated) |
| KV memory | "Which concept is this?" (formula not recalled) | E | 1 | 11-23 | — |
| KV memory | 4× users: need (× 4) vs fix (smaller per token) | B | 1 | 11-23 | — |
| KV memory | Quantization: only weights, missed KV cache | A | 1 | 11-23 | 11-23 (same day) |
| GPU | Why K/V is read per user: mixed prefix-cache hit with decode reads | A | 1 | 11-23 | — |
| Model Runner | Chunked prefill: pictures prefill and decode as alternating steps (they share one step, different users) | A | 2 | 11-23 (×2) | — |
| Model Runner | Chunked prefill → long-prompt TTFT slower: right conclusion, reason missing (more steps) | B | 1 | 11-23 | 11-23 (restated) |

\* 11-13: not reviewed, counted in the source as a forgotten check. `?` = from the archive, date not exact.

## How to update (each review)

1. For every Miss / Partial: +1 on its row (or add a row), add the date.
2. For a right answer: write the date in *Last right* (no reset).
3. Recount the pattern totals. Copy the top 2 patterns into project memory.
