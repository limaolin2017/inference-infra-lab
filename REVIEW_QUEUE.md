# Review Queue — Unclear Points

This file tracks **concepts that caused confusion**, not every question asked. Learning progress is evidence-based.

**Review rule:** Prioritize the **Scheduler → KV Manager → Model Runner** main path. Ask a short unseen question when it helps learning, before showing notes or hints. Record Pass / Partial / Miss after the attempt. **The older EAGLE quiz is optional (P2), not a blocker.**

## 2026-11-09 — next P0 reviews (no formal pass)

| Topic | Observed answer / uncertainty | State | Next check |
| --- | --- | --- | --- |
| Scheduler: existing Running requests vs `max_num_seqs` | Q1: with A and C already Running (`max_num_seqs=2`), B Waiting, token budget 6 and one reclaimable `ref_cnt=0` cache block, learner first predicted A continues, then incorrectly changed to A cannot continue. Guided correction: A and C can remain Running, B cannot be admitted to the already-full running set. | **Partial · L2 practice; no source/test verification** | **2026-11-10 or later**: fresh case where running slots and token budget give different limits; state who computes and why |
| KV: eviction vs preempting a Running request | Q2: when all KV blocks are referenced, learner initially proposed waiting and forgot the meaning of eviction. After a short explanation, understood that unreferenced reusable cache can be evicted, but a referenced active block cannot simply be reclaimed. In an illustrative A/C case, preempting C *may* free space for A, depending on Scheduler policy; learner understood this after guidance. | **Partial · guided; request-selection policy unverified** | **2026-11-10 or later**: new example with live refs and A/C state; choose eviction, wait or preemption, and explain why the selected request can be stopped |
| Worker request-state reuse vs KV Cache reuse | In the SchedulerOutput → Model Runner discussion, learner explained why A need not receive its full prompt each step by saying it already has KV Cache, and asked whether a cache hit occurs in Scheduler or Model Runner. **Correction:** `scheduled_cached_reqs` means the worker already has A's request metadata/state; it sends updates instead of repeating full request data. This is **different from** reusing attention K/V. For a newly admitted request, local **prefix-cache hit lookup** is done during Scheduler scheduling via `KVCacheManager.get_computed_blocks()`; normal continuation of A's own allocated KV does not require a fresh prefix lookup each step. Preempted requests may need full worker state re-sent on re-admission. Source: [SchedulerOutput](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/core/sched/output.py) · [Model Runner add/update](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/worker/gpu/model_runner.py#L1280-L1348) · [KV manager lookup](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/core/kv_cache_manager.py#L282) | **P0 · Partial / guided explanation; source shown by assistant, no independent L3 or test** | **2026-11-10 or later**: unseen A (continuing), B (new), C (preempted then readmitted) scenario. Predict which sends full request data, which sends only updates, and where prefix KV lookup happens. Explain the two kinds of reuse in your own words. |
| Block allocation vs position/slot computation | An earlier assistant graded the learner's guess "half right": the Model Runner needs the new token's position (correct) but does not allocate KV blocks (exact guess not recorded). Guided: Scheduler + KV Cache Manager allocate blocks; the Model Runner uses position + Block Table to find the slot. The learner then restated it correctly in their own words (lengths differ and grow; fixed-size non-contiguous physical blocks; position + Block Table gives the slot; K and V are written, not the token). Source shown by assistant: [prepare_attn()](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/worker/gpu/model_runner.py#L1658-L1678) · [slot_ids kernel](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/worker/gpu/block_table.py#L402-L409) · [concept note](concepts/block-table-and-slot-mapping.md). | **Partial · guided; no independent L3 or test** | **2026-11-10 or later**: unseen case — give a position, `block_size` and a Block Table; find the slot, and say who allocates the block and who computes the slot. |
| Scheduler: WAITING is skipped after a preemption | Case: `token_budget=10`; A and C Running; A needs a new block, all blocks referenced, so C is preempted; B Waiting with a 6-token prompt. Learner: B cannot be scheduled because the Scheduler preempts C (or A itself) to make space for A. Conclusion correct; reason incomplete. **Source (assistant-shown):** FCFS picks the victim as `self.running[-1]` ([L946–L954](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/core/sched/scheduler.py#L946-L954)); A preempts itself only if no other victim is left. Then `if not preempted_reqs` ([L1065–L1066](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/core/sched/scheduler.py#L1065-L1066)) skips the whole WAITING block in that step, even when token budget remains. | **Partial · guided; source shown by assistant, no independent L3** | **2026-11-10 or later**: unseen case with leftover budget and free blocks, with vs without a preemption earlier in the step; predict whether a Waiting request is admitted and point to the gate line. |
| Preemption unit: a request, not a block (**recurring**, also 2026-11-05) | All 10 blocks referenced; A needs 1 block. Learner: `allocate_slots()` returns `None` (correct), then the Scheduler "stops one block". Correction: it preempts a whole **request** (FCFS: last in `running`), which frees all of that request's blocks; A then retries. | **Partial · recurring confusion** | **2026-11-10 or later**: unseen case where the victim holds 3 blocks and A needs 1; ask how many blocks become free and what happens to the victim's KV. |

**Review method:** No more than one brief correction now; revisit with a different unseen case after time to digest. Do not award an L1/L2-H or A pass for guided answers. Do not start a deep preemption-policy detour during morning review.

| Topic / source of confusion | Review question focus | Last session | State | Next review |
| --- | --- | --- | --- | --- |
| EAGLE trailing-block drop | Why identical block tokens may not prove identical draft KV | 2026-11-04 | Understood conceptually; retention untested | 2026-11-05 |
| Lookahead Hash: what is "ahead"? | Identify the first token after a full block | 2026-11-04 | Corrected and explained | 2026-11-05 |
| Lookahead Hash: hash vs. KV | Is the next token's **ID** included, or is its Draft KV compared? | 2026-11-04 | Corrected with guidance; high-priority retest | 2026-11-05 |
| Target KV vs. EAGLE Draft KV | Which is changed by a different token after a shared prefix? | 2026-11-04 | Explained; retention untested | 2026-11-05 |
| CPU OffloadingConnector reload | Why could a cache layer load data another layer excluded from local hits? | 2026-11-04 | **Not yet learned; P2 side quest** | Only if current-main issue persists |

## Morning 2026-11-05 follow-up — park for evening

| Topic | Next unseen check | Morning evidence | State |
| --- | --- | --- | --- |
| Eviction vs preemption | Given live refs and reusable cached blocks, predict whether to evict, wait, or preempt, and **which request** is stopped | Initially confused eviction/preemption and thought a single block was preempted; later correctly chose unreferenced Block 2 and preempting running C in a guided A/C case | **L2 practice (guided); delayed independent check pending** |
| Scheduler token budget vs KV space | New case with token budget still free but no allocatable KV blocks; predict which requests are scheduled, not merely arithmetic | Correctly allocated budget 4 as A=2, B=2; understood waiting vs preempt after explanation | **L2 practice (guided); source/CPU trace pending** |
| Hidden states / QKV / attention | Explain why current Q is **not a user Request**, why each Transformer layer creates K/V from its hidden states, and why historical K/V suffice for next-step attention | Good explanation of K/V reuse; corrected Q=Request and “hidden states do not enter attention” | **L1–L2 practice (guided); fresh case later** |

**Timebox:** morning review already ended. Do **not** start these now. Take only a small sample in the 20–30 minute evening session; carry unfinished items forward.

## Main-path review queue (P0, next)

These are *future* proof tasks, not scored questions yet. Start with items 1 and 2, rather than studying more EAGLE edge cases.

1. Explain one prefill and one decode path through `Scheduler → KVCacheManager → ModelRunner` without prompts.
2. Given a model and batch shape, derive approximate K/V memory and identify the memory limit.
3. **Deferred until suitable GPU access:** repeatable single NVIDIA GPU serving with TTFT, TPOT and throughput.
4. **Deferred until suitable GPU access:** explain an actual GPU profile before choosing an optimization.
5. If targeting serving/platform work, demonstrate Linux/Docker/K8s and service metrics.

Keep the earlier [2026-11-05 EAGLE quiz](reviews/2026-11-05.md) unchanged as an **optional P2 review**. See [SKILL_MAP.md](SKILL_MAP.md) for the trunk.

## Review outcome rules

- **Pass:** independently explains the cause and applies it to a fresh example.
- **Partial:** correct conclusion but confuses mechanism (e.g. token ID vs. Draft KV).
- **Miss:** wrong conclusion or cannot explain.
- **Unattempted:** no review yet; do not claim a pass.

Do **not** pre-fill answers in any blind quiz, including the [optional EAGLE quiz](reviews/2026-11-05.md). Record explanations **after** a learner response.

Use at most **3 P0 questions / 15 minutes in the morning** and optional **10–15 minutes in the evening**. If a topic passes twice on different days, move it to a less frequent review.
