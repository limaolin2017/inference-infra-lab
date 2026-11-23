# Review Queue — Unclear Points

This file tracks **concepts that caused confusion**, not every question asked. Learning progress is evidence-based.

**Review rule:** Prioritize the **Scheduler → KV Manager → Model Runner** main path. Ask a short unseen question when it helps learning, before showing notes or hints. Record Pass / Partial / Miss after the attempt. **The older EAGLE quiz is optional (P2), not a blocker.**

## 2026-11-23 — Scheduler 3 parts re-learned (guided, no formal pass)

Full log: [journal/2026-11-23.md](journal/2026-11-23.md).

| Topic | Observed answer / uncertainty | State | Next check |
| --- | --- | --- | --- |
| Scheduler vs `kv_cache_manager` (**new**) | Said `kv_cache_manager` decides who goes to waiting / running. Correct: the Scheduler decides; `kv_cache_manager` only gives out / takes back KV blocks. | **Miss → restated** | 2026-11-24: "Who decides, who only answers? What does `allocate_slots` return when there are no blocks?" |
| What triggers preemption (**new**) | Thought a new waiting C can preempt running B. Correct (FCFS): only a running request that needs one more block with no free blocks. | **Partial → restated** | 2026-11-24: two cases — new C can't fit vs running A needs a block. Which one preempts? |
| Which request gets preempted (**new**) | Said "Scheduler preempts" (right) but not who. Correct (FCFS): the last request in `running`; can be A itself. | **Due** | 2026-11-24: running = [A, B, D], A needs a block — who is preempted, where does it go, what happens to `num_computed_tokens`? |
| `add_request` vs `prepend_request` | Front right; method name unknown. | **Miss (name)** | 2026-11-24: "Which method for a new request, which for a preempted one?" |
| Big batch → TPOT: the mechanism (**new**) | 50 users decoding → said TPOT (right) "because it is decode, not the first token" — reason missing. Mechanism: each step reads all users' K/V; weights shared. | **Partial** | 2026-11-24: "Batch 1 → 50: which read grows per step, weights or K/V?" (also an 11-10 row) |
| Two kinds of quantization (**new, learner asked to review**) | 4× users question: first wrote "× 4" (= memory needed, not the fix). Then: "lower precision, 8-bit or 4-bit quantization" (right direction, but only weights). Restated: KV cache quantization (FP8, the `bytes` factor) vs weight quantization (bigger KV budget); both are quantization. Quick check "which changes `bytes`?" → method 1 (KV cache) — right. | **Partial → restated · check right** | 2026-11-24: "Which one changes `bytes` in the formula? Which one makes the KV budget bigger? Name one vLLM setting." |
| KV per-token formula: the leading 2 (**new**) | Step 1 (Qwen2.5-0.5B): "don't know which concept". Llama-3-8B: wrote 32 × 8 × 128 × 2 (missed the 2 for K and V) → 64 KiB. Asked why × 2; explained (K and V both cached). | **Miss → restated** | 2026-11-24: new model config, learner writes the full formula first, then the number. |
| KV heads vs KV cache (**new, question**) | Asked the difference. Answer: KV heads = number in the model config; KV cache = stored data that grows per token. | **Explained** | 2026-11-24: "GQA cuts KV heads 4× — what happens to the KV cache?" |
| KV budget steps 2–4 (**new**) | Picture shown; learner said "understood", but steps 2–4 not computed yet; Llama-3-8B final (128 KiB) not given. | **Due** | 2026-11-24: finish 64 × 2, then budget → max tokens → 4K requests. |
| 3 key parts | Re-learned with animation; `waiting` / `running` right. | **Right · guided · same day** | 2026-11-24: name all 3 alone, one line each. |

## 2026-11-19 — studied outside the project chat (self-reported, no answers saved)

Full log: [journal/2026-11-19.md](journal/2026-11-19.md).

| Topic | Observed answer / uncertainty | State | Next check |
| --- | --- | --- | --- |
| Scheduler: 3 key parts (**new**) | Next day recalled only `waiting` (a deque); forgot the other two. | **Miss (forgot)** | 2026-11-20: re-learn, then "Name the 3 parts and say what each holds." |
| `waiting` = deque (**new**) | Named, not explained. Source: `FCFSRequestQueue` subclasses `deque`; add → end, pop → front, `prepend_request` → front. | **Partial** | 2026-11-20: "A preempted request goes back to Waiting — front or end? Which method?" |
| Blocks (review) | Result not recorded. | **Due** | 2026-11-20: fresh `slot_mapping` case (rows below). |
| No free blocks (review) | Result not recorded. | **Due** | 2026-11-20: "Free queue empty — what does the Scheduler do, which request, what happens to its progress?" |

## 2026-11-17 — `slot_mapping` review + KV memory formula (no formal pass)

Full log: [journal/2026-11-17.md](journal/2026-11-17.md). Case: `block_size` 4, table `[7, 2, 5]`.

| Topic | Observed answer / uncertainty | State | Next check |
| --- | --- | --- | --- |
| `slot_mapping` vs `block_table` | Mixed them up. Correct: table = per request, for **read**; slot_mapping = per new token, for **write**. | **Miss → restated** | 2026-11-18+: "Which one does attention read, which one does `store_kvcache` use?" |
| Use the **physical** block | pos 6 → answered 6 (used logical block). Correct: 10. | **Miss → restated** | 2026-11-18+: fresh table and pos. |
| offset = **remainder** (recurring, 2×) | pos 11 and pos 12: used the quotient. Correct: 23 and 0. | **Miss → restated** | 2026-11-18+: two fresh positions, one at a block edge. |
| pos counts from 0 | pos 10 → answered 9 (as token index). Correct: 22. | **Miss → restated** | Fold into the rows above. |
| Decode: size of `slot_mapping` | Took it as total tokens. Correct: **1** per request. | **Miss → restated** | 2026-11-18+: decode batch of 3 requests — how many numbers? |
| pos 12 → new block | Idea right (logical 3 not in table → allocate). | **Partial** | **First question next session:** no free blocks — what does the Scheduler do? |
| MHA / GQA / MQA (**new**) | Thought the difference was blocking; thought KV heads were "split" from K. Correct: different **KV head count**; heads are projected with weights. | **Corrected** | 2026-11-18+: per-token bytes for a GQA model with new numbers. |
| Why vLLM reserves KV at startup (**new**) | Thought it was for warm-up. Correct: slow allocation, fragmentation, fixed capacity for the Scheduler. | **Corrected** | 2026-11-18+: "Why not allocate when a request arrives?" — give 2 reasons. |

## 2026-11-16 — review results (no formal pass)

Three 2026-11-13 rows reviewed with fresh cases. Full log: [journal/2026-11-16.md](journal/2026-11-16.md).

| Topic | Observed answer / uncertainty | State | Next check |
| --- | --- | --- | --- |
| `max_num_batched_tokens` direction (**4th check, recurring**) | Independent case: flipped both (User 1 lower, User 2 raise). Reason "more tokens per step → stutter" was right but put on the wrong user. After Chinese explanation: "raise = one step fits more prefill" → right. | **Miss → restated** | 2026-11-17+: learner fills "第一个 token 慢，我 ___ 它，因为 ___", then a fresh case with a different symptom (e.g. long-prompt users vs chat users), no hint. |
| evict: when the hash is removed (2nd check) | Said "after N's K/V is computed". Correct: in `get_new_blocks()`, right after `popleft_n()`, before GPU work. K/V not erased, overwritten later — right. | **Partial** | 2026-11-17+: "Scheduler step vs GPU step — which one removes the hash, which one overwrites K/V? Why can't the hash wait?" |
| Free queue end: the reason (3rd check) | Position right (end); reason forgotten, restated after one reminder. | **Partial · restated** | 2026-11-18+: one fresh case, reason first. |
| Shared block `ref_cnt` (2nd check) | 3 → 2 (preempt) → 1 (finish); not in free queue while A runs. | **Right** | Less frequent; fold into a later mixed case. |
| `slot_mapping` (**new**) | After a static picture: pos 2, `block_size` 4, table `[7, 2]` → 30 (right). Needed help reading `Block Table[i] = b` and "5 ÷ 4 = 1". | **Right · guided · same day** | 2026-11-17+: new numbers (e.g. `block_size` 4, table `[5, 0, 3]`, pos 9) — slot? Who writes the table, who computes the slot? |

## 2026-11-13 — review results (no formal pass)

All three 2026-11-11 rows below were reviewed today with fresh cases. Answers were right but **guided** (taught in earlier sessions). Full log: [journal/2026-11-13.md](journal/2026-11-13.md).

| Topic | Observed answer / uncertainty | State | Next check |
| --- | --- | --- | --- |
| WAITING gate after preemption (5th check) | Right reason for the first time: "preemption in this step → whole Waiting skipped". Follow-up (no preemption, Waiting C → E, C can't fit): right — not skipped, but C blocks the head and E waits (FCFS `break`). | **Right · guided** | 2026-11-16+: mixed case — one step with preemption, next step without; Waiting head fits vs doesn't fit. |
| Preempt: request field vs block field (3rd check) | Right: `num_computed_tokens` 40 → 0 (request); `ref_cnt` 1 → 0 (block); K/V kept. | **Right · guided** | 2026-11-16+: one more fresh case to confirm. |
| Shared block `ref_cnt` after one user is preempted (**new**) | c1 shared by C and D (`ref_cnt` 2), C preempted: said `ref_cnt` → 0 (wrong, should be 2 → 1); free queue: self-corrected to "no, D still uses it". | **Partial** | 2026-11-14+: block shared by 3 requests, 1 preempted and 1 finishes — `ref_cnt` after each event, and when does it enter the free queue? |
| Free queue: front vs end (2nd check) | Right: X (no hash) → front, Y (hash) → end; N takes X, E reuses Y. Explained why in own words: at the front a new request would take Y and overwrite its K/V. | **Right · own words** | 2026-11-16+: one more fresh case; then move to less frequent review. |
| `max_num_batched_tokens` direction | Not reviewed today. | **Due** | 2026-11-14: independent two-complaint case, no hint. |
| **Code check:** which line takes blocks in `get_new_blocks()` (**new**) | Pointed to `on_block_allocated` (metrics only). Real line: `free_block_queue.popleft_n()` (L696). | **Partial · guided** | 2026-11-14: show the function, learner points to the take line and says why `popleft` = front. |
| **Code check:** `free_block_queue` is a queue, not a function (**new**) | Called it "the function that takes blocks". | **Corrected** | 2026-11-14: "In `self.free_block_queue.remove(block)`, which part is the thing and which is the action?" |
| evict vs preempt; what evict removes (**new**) | Forgot evict; then said evict "fully clears K/V". Correct: evict removes the hash label (lookup entry); K/V is overwritten later by the new owner. Self-noted: free / preempt keep the hash. | **Partial · guided** | 2026-11-14: "C is preempted, then a new request takes one of C's blocks. When is the hash removed, and is K/V erased?" |
| `free_blocks()`: hashed block → `append_n` → end (**new**) | Said `prepend_n`. Idea (hash → end) was right; method name mixed up. Then gave front for a no-hash block. | **Miss (name) → restated** | 2026-11-14: "Which method puts a block at the front, which at the end? Which one does a hashed block use?" |
| Model Runner `positions` (from 2026-11-10 row) | A 6 computed + decode 1, C new 3 → "6, 0, 1, 2". | **Right · guided** | 2026-11-16+: three requests (one old, two new, one with chunked prefill) without a table template. |

## 2026-11-11 — review results + BlockPool (no formal pass)

| Topic | Observed answer / uncertainty | State | Next check |
| --- | --- | --- | --- |
| WAITING gate after preemption (**recurring, 4th**) | Case 2: B not admitted (right) "because D is in front of B" (wrong — D does not run either). Restated after gate animation: preemption → Waiting skipped. | **Partial · guided** | 2026-11-12+: fresh case where the Waiting head is *not* the preempted request; ask why nobody is admitted and point to `if not preempted_reqs`. |
| Preempt: request field vs block field (**recurring, 2nd**) | Answered `ref_cnt` as D's reset field; said "D goes to Waiting" when asked where the blocks go. Restated: progress (`num_computed_tokens`) belongs to the request, `ref_cnt` to the block. | **Miss → restated** | 2026-11-12+: "after preempt, list one request field and one block field that change, with old → new values." |
| `max_num_batched_tokens` direction | Forgot; correct only after slider animation + Chinese explanation. | **Correct · guided** | 2026-11-12+: independent two-complaint case, no hint. |
| Free queue: front vs end | Did not give a position for a hashed block; then asked "at the front it can't come out?". Correct: `touch()` takes from any position; the end protects the block from `get_new_blocks()` (front only). Restated correctly. | **Partial · guided** | 2026-11-12+: two freed blocks, one with hash, one without; then one new request and one same-prefix request arrive — which block does each take? |
| Block life cycle: `ref_cnt` 0, hit reuse, K/V kept | Correct (`ref_cnt` 0; E reuses Y, +1, leaves free queue; K/V kept). | **Pass · same day** | One more next-day check, folded into the row above. |

## 2026-11-10 — memory-bound / batching (no formal pass)

**Note:** the 2026-11-09 rows below were **not** reviewed on 2026-11-10; they are still due.

| Topic | Observed answer / uncertainty | State | Next check |
| --- | --- | --- | --- |
| Where the memory-bound data path is | Said data moves CPU → GPU. Correct: HBM → SRAM/compute cores inside the GPU; CPU ↔ GPU is PCIe (KV offload). | **Partial · guided** | 2026-11-11 or later: "During decode, what moves, from where to where?" |
| Batching: what is shared | Said weights **and** KV are loaded once. Correct: weights shared; KV per request. | **Partial · guided** | 2026-11-11 or later: batch 8 — how many weight reads vs KV-cache reads per step? |
| Throughput vs per-user speed (TPOT) | Picked the right batch side for chat, but reason was "throughput up → user sees text faster". | **Partial · guided** | 2026-11-11 or later: throughput doubles, TPOT unchanged — did each user get faster? |
| Decode reads weights too | Listed only KV cache. | **Partial · guided** | Fold into the first row's check. |
| `input_ids` vs `positions` | Thought `input_ids` are 1, 2, 3 numbering. Correct: vocabulary IDs (which token); `positions` = place in its own request (from 0). | **Partial · guided** | 2026-11-11 or later: A has 6 computed, decodes 1; B prefills 3 — write both arrays. |
| `max_num_batched_tokens` direction (TTFT vs TPOT) | Said lower it to fix slow first token; mixed it with batch size and memory-bound. Correct: raise it → fewer steps to first token; cost: TPOT spikes for running users. | **Miss · guided** | 2026-11-11 or later: two complaints (slow first token vs stutter mid-answer) — which way for each, and why? |
| Chunked prefill: how chunks combine | Said chunk results are "summed at the end". Correct: each chunk writes K/V; next chunk attends to earlier K/V. | **Partial · guided** | Fold into the row above. |

**2026-11-10 review of 2026-11-09 rows:** WAITING gate after preemption → **Miss again (3rd)**, keep top priority with a fresh case 2026-11-12+. Block life cycle → `ref_cnt` 0 / free queue / reuse **Correct**, evict case **Partial**. Preempt freed vs reset → **Partial** (named `ref_cnt` instead of `num_computed_tokens`; mixed block count with `ref_cnt`). Not yet re-tested: `max_num_seqs` vs Running, eviction vs preemption choice, worker-state vs KV reuse, Block Table slot.

## 2026-11-09 — next P0 reviews (no formal pass)

| Topic | Observed answer / uncertainty | State | Next check |
| --- | --- | --- | --- |
| Scheduler: existing Running requests vs `max_num_seqs` | Q1: with A and C already Running (`max_num_seqs=2`), B Waiting, token budget 6 and one reclaimable `ref_cnt=0` cache block, learner first predicted A continues, then incorrectly changed to A cannot continue. Guided correction: A and C can remain Running, B cannot be admitted to the already-full running set. | **Partial · L2 practice; no source/test verification** | **2026-11-10 or later**: fresh case where running slots and token budget give different limits; state who computes and why |
| KV: eviction vs preempting a Running request | Q2: when all KV blocks are referenced, learner initially proposed waiting and forgot the meaning of eviction. After a short explanation, understood that unreferenced reusable cache can be evicted, but a referenced active block cannot simply be reclaimed. In an illustrative A/C case, preempting C *may* free space for A, depending on Scheduler policy; learner understood this after guidance. | **Partial · guided; request-selection policy unverified** | **2026-11-10 or later**: new example with live refs and A/C state; choose eviction, wait or preemption, and explain why the selected request can be stopped |
| Worker request-state reuse vs KV Cache reuse | In the SchedulerOutput → Model Runner discussion, learner explained why A need not receive its full prompt each step by saying it already has KV Cache, and asked whether a cache hit occurs in Scheduler or Model Runner. **Correction:** `scheduled_cached_reqs` means the worker already has A's request metadata/state; it sends updates instead of repeating full request data. This is **different from** reusing attention K/V. For a newly admitted request, local **prefix-cache hit lookup** is done during Scheduler scheduling via `KVCacheManager.get_computed_blocks()`; normal continuation of A's own allocated KV does not require a fresh prefix lookup each step. Preempted requests may need full worker state re-sent on re-admission. Source: [SchedulerOutput](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/core/sched/output.py) · [Model Runner add/update](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/worker/gpu/model_runner.py#L1280-L1348) · [KV manager lookup](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/core/kv_cache_manager.py#L282) | **P0 · Partial / guided explanation; source shown by assistant, no independent L3 or test** | **2026-11-10 or later**: unseen A (continuing), B (new), C (preempted then readmitted) scenario. Predict which sends full request data, which sends only updates, and where prefix KV lookup happens. Explain the two kinds of reuse in your own words. |
| Block allocation vs position/slot computation | An earlier assistant graded the learner's guess "half right": the Model Runner needs the new token's position (correct) but does not allocate KV blocks (exact guess not recorded). Guided: Scheduler + KV Cache Manager allocate blocks; the Model Runner uses position + Block Table to find the slot. The learner then restated it correctly in their own words (lengths differ and grow; fixed-size non-contiguous physical blocks; position + Block Table gives the slot; K and V are written, not the token). Source shown by assistant: [prepare_attn()](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/worker/gpu/model_runner.py#L1658-L1678) · [slot_ids kernel](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/worker/gpu/block_table.py#L402-L409) · [concept note](concepts/block-table-and-slot-mapping.md). | **Partial · guided; no independent L3 or test** | **2026-11-10 or later**: unseen case — give a position, `block_size` and a Block Table; find the slot, and say who allocates the block and who computes the slot. |
| Scheduler: WAITING is skipped after a preemption | Case: `token_budget=10`; A and C Running; A needs a new block, all blocks referenced, so C is preempted; B Waiting with a 6-token prompt. Learner: B cannot be scheduled because the Scheduler preempts C (or A itself) to make space for A. Conclusion correct; reason incomplete. **Source (assistant-shown):** FCFS picks the victim as `self.running[-1]` ([L946–L954](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/core/sched/scheduler.py#L946-L954)); A preempts itself only if no other victim is left. Then `if not preempted_reqs` ([L1065–L1066](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/core/sched/scheduler.py#L1065-L1066)) skips the whole WAITING block in that step, even when token budget remains. | **Partial · guided; source shown by assistant, no independent L3** · evening 11-09: **Partial again** — answer right (B not admitted), reason wrong ("A still runs and needs a block"); after one picture restated the rule correctly | **2026-11-10 or later**: unseen case with leftover budget and free blocks, with vs without a preemption earlier in the step; predict whether a Waiting request is admitted and point to the gate line. |
| Preemption unit: a request, not a block (**recurring**, also 2026-11-05) | All 10 blocks referenced; A needs 1 block. Learner: `allocate_slots()` returns `None` (correct), then the Scheduler "stops one block". Correction: it preempts a whole **request** (FCFS: last in `running`), which frees all of that request's blocks; A then retries. | **Partial · recurring confusion** · evening 11-09: **Correct** (3 blocks freed, progress 0, back to Waiting) — same day, so keep one more next-day check | **2026-11-10 or later**: unseen case — victim holds N blocks, A needs fewer; how many blocks are freed, where does the victim go, and is its KV kept? (2026-11-09 follow-up was Partial: thought only 1 of 3 blocks is freed and the rest of C's KV stays. Truth: all freed, `num_computed_tokens = 0`, back to Waiting, recompute — [`_preempt_request()`](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/core/sched/scheduler.py#L1853-L1896).) |
| Block life cycle: `ref_cnt` 0 → free queue | Evening Q5: X shared by A and B (`ref_cnt` 2), both finish. Learner: `ref_cnt` 0 (correct), old KV still there (correct), but X "stays where it is" — missed that it **returns to the free queue** where any request can take it (reuse on hit, else evict). | **Partial · guided** | **2026-11-10 or later**: unseen case — a block hits `ref_cnt` 0, then a new request with the same prefix vs a different prefix arrives; what happens to the block each time? |
| Preempt: what is reset vs what is freed | Evening Q6: learner said C's **KV cache is cleared**. Fact (source): `_free_request_blocks()` (line 1871) returns blocks without clearing data; `num_computed_tokens = 0` (line 1876) resets the **progress counter**. Restated correctly after one picture. | **Partial · guided** | **2026-11-10 or later**: ask "after preempt, which two things change: the blocks and which field?" and point to both lines without hints. |

**Review method:** No more than one brief correction now; revisit with a different unseen case after time to digest. Do not award an L1/L2-H or A pass for guided answers. Do not start a deep preemption-policy detour during morning review.

Older P2 EAGLE rows and the 2026-11-05 follow-ups: [reviews/archive-2026-11-04-05.md](reviews/archive-2026-11-04-05.md).

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
