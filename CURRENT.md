# Current — Read This First

**Last reviewed:** 2026-11-26 (project chat + voice: preemption and quantization review; 11-25 no session)  
**Goal:** Build job-ready inference-core skill through focused, verifiable upstream work.  
**Hardware:** No owned or continuously available GPU. **Google Colab free GPU is an optional, availability-dependent way to run short NVIDIA tests.** Never require it for CPU core work.

## Main line (set 2026-11-17)

1. **KV cache memory calculation and capacity planning** (KV Cache Handbook pp. 7–9) ← **current, just started** · [KV cache memory](concepts/kv-cache-memory.md)
2. Prefill / decode performance and bottleneck analysis
3. Scheduler batching and preemption logic
4. Benchmark and profiling

The request-path trace below is the background for items 1 and 3.

## Status board — mastered / gap / unclear (updated 2026-11-26)

Update this board at every session close. Copy kept in project memory.

| Area | Mastered (can do) | Gap (cannot do alone yet) | Unclear (still confused) |
| --- | --- | --- | --- |
| KV memory (item 1) | `gpu_memory_utilization` = share of total memory, fixed at startup; `config.json` = the model's, `block_size` = vLLM's (guided 11-24); [`allocate_kv_cache`](concepts/kv-cache-memory.md) taught 11-24; Formula `2 × layers × KV heads × head_dim × bytes`; MHA / GQA / MQA = KV head count; why × 2; KV heads vs KV cache; rough estimate idea; **leading 2 written first try (11-24)**; KV cache quantization changes `bytes` (first try 11-24); weight quantization → bigger KV budget (guided 11-24, guided again 11-26); both quantization places named first try (11-26) | Last factor alone (bits vs bytes, 2×: FP16 = 2 bytes); KV budget steps 2–4 alone; find the 4 fields in a `config.json` alone (missed `layers` 11-24); say alone why weight quantization fits more users (Partial 2×); `block_size` 16 → 32 effect | Bits vs bytes; **`block_size` vs `block_bytes` (count vs size; input vs result) — ended unclear 11-24** |
| `slot_mapping` | 3-step rule; pos 12 needs a new block; read vs write (Block Table read, `slot_mapping` write — first try 11-24) | Fresh cases without help; say alone who builds (Model Runner) / uses (attention) it | offset = remainder (missed 2×); use the physical block; pos from 0; `slot_mapping` vs `block_table` (per token slot vs per request blocks, 2×); who builds / uses it |
| BlockPool | `ref_cnt`, free queue front / end, evict vs preempt (guided) | Independent code trace | Free-queue "why end" reason; evict hash timing |
| Scheduler | Token budget, preemption, Waiting gate (guided); 3 parts; **Scheduler decides, not `kv_cache_manager` (first try 11-24, 11-26)**; **FCFS preempts the last in `running` (first try 11-24)**; preempted → front of `waiting` (11-26 with a front/back prompt); preempted request's blocks are freed → recompute (restated 11-24) | Independent `schedule()` trace + CPU test; `add_request` vs `prepend_request` names; say "front" without a prompt; requester last in `running` → kicks **itself** out (Miss 11-26, own words after) | Preempt frees blocks (said "blocks stay" 11-24); **preemption trigger — thought a new request D forces a kick-out (Miss 2×: 11-23, 11-26)** |
| Model Runner | `input_ids` / `positions` (guided) | Find `positions` / `slot_mapping` in source alone | Chunked prefill details |
| Prefill / decode (item 2) | Long prompt → slow TTFT; raise `max_num_batched_tokens` → faster TTFT, cost TPOT; **reverse case (chat users stutter → lower) direction + cost right first try, no hint (11-24)**; chunked prefill = one step with a prefill chunk + others' decode (11-24); first token at the end of the last chunk (restated); batching shares weights, not K/V → still mostly memory-bound at 50 (own words 11-24); **[`max_num_seqs`](concepts/max-num-seqs.md) taught 11-24: new request waits, never preempts (first try); many users → lower it, cost TTFT (first try)** | Say alone: TTFT = compute-bound, TPOT = memory-bound (**swapped 2×**: 11-23, 11-24); reasons in own words first try ("shorter step", not "more compute for decode"); `max_num_seqs` mechanism first try (each step reads every running user's K/V — missed 2×); pick the right knob in a mixed case (missed 11-24) | Compute vs load wording (prefill computes, decode reads); two side-by-side causes linked as cause → effect (3×); tokens per step vs requests per step (which knob owns what) |
| Benchmark / profiling (item 4) | — | Public, rerunnable benchmark; profiling | Not started |

**Formal passes:** 0. All answers so far are guided.

### Order of work (agreed 2026-11-23)

No good local machine for tests yet, so: **(1) concepts first until the run-gate below → (2) one CPU test of the Scheduler or BlockPool (pytest, runs on a laptop; first independent public proof) → (3) GPU benchmark on Colab** (TTFT / TPOT, startup log vs KV formula).

### Colab gate — measurable (agreed 2026-11-23)

Start the first Colab GPU run when **all 3** pass. Pass = closed-book, first try, no hints (a hint = guided, does not count).

| Check | Task | Pass if | Status 11-24 |
| --- | --- | --- | --- |
| A. Capacity (容量估算) | Unseen `config.json` + GPU size: find the 4 fields, write the full formula, say budget → max tokens → max 4K requests (assistant does the arithmetic) | All steps right on **2 models, 2 different days** | ~50%: leading 2 right (11-24); bits vs bytes slip; steps 2–4 not done |
| B. TTFT vs TPOT (延迟检查) | Explain what limits each; 3 "user complains about X — which knob?" cases | Explanation right + **≥ 2 / 3** cases | ~40%: all 3 knobs taught; 2 knob cases right first try (reasons partial); mixed case missed (wrong knob); compute/memory-bound swapped 11-24 |
| C. Prediction (运行前预测) | Before the run, write the expected KV cache size and block count for the Colab model | Written **before** running | After A |

Estimate: **2–3 weeks** at ~4 sessions / week (A ≈ 1 week, B ≈ 1–2 weeks) → about mid-December. The CPU test does not block Colab; order chosen later.

### When to start running (agreed 2026-11-17)

Run vLLM for the first time when **both** are true:

1. Item 1: can calculate alone how many requests a real model fits.
2. Item 2: can explain alone what limits TTFT and what limits TPOT.

The first run checks concepts (e.g. the KV formula vs the vLLM startup log); it is not meant to produce a result.

## Request-path trace (background)

**Trace one request through `Scheduler → KV Cache Manager / BlockPool → Model Runner`.**

| Stage | Status (all guided — no independent L3 trace yet) | Concept notes |
| --- | --- | --- |
| 1. Scheduler | Concepts mostly learned: token budget, `max_num_seqs`, preemption, Waiting gate | [scheduler](concepts/scheduler-and-cache-policy.md) |
| 2. KV Cache Manager / BlockPool | Concepts + **code check done 2026-11-13** (guided, learner-located) | [BlockPool life cycle](concepts/blockpool-block-life-cycle.md) · [Block Table / slot](concepts/block-table-and-slot-mapping.md) |
| 3. Model Runner | **In progress** — `input_ids` / `positions` right on 2026-11-13 (guided); `slot_mapping` concept right on 2026-11-16 (guided), arithmetic gaps on 2026-11-17 review; chunked prefill concept only; source not yet located | [Model Runner inputs](concepts/model-runner-inputs-and-chunked-prefill.md) |
| After all 3 | Independent `schedule()` trace → one CPU check → choose a real Issue | — |

Side concept (GPU): [memory-bound decode and batching](concepts/memory-bound-and-batching.md).

## Next session

**Question rule (from 11-26):** give the **full setup first** (what is fixed, who is running, in what order, what is needed), then ask. Mid-scenario questions confused the learner 6× on 11-26.

0. **Start with a re-teach (Chinese + egg carton picture):** `block_size` vs `block_bytes` — ended unclear 11-24, not reached 11-26. Then "which one do you set, which one does vLLM calculate?" and "16 → 32: what changes?"
0b. **Preemption re-check (11-26 misses, full setup, no hint):** "D arrives, no free blocks — what happens to D? Does anyone get kicked out?"; "`running` = X, Y, Z, Z needs a block — who leaves, and where does it go?"; "Weights 16 GB → 8 GB — what changes for the KV cache?"
1. **Review (still due from 11-24, from [REVIEW_QUEUE.md](REVIEW_QUEUE.md), ≤ 6 questions, no hints):** TTFT compute-bound vs TPOT memory-bound (先算后读); lower the knob — does chat user A's K/V read still grow? (side-by-side causes); fill-in "prefill ___ K/V, decode ___ K/V"; FP8 KV cache last factor + a new model's full formula; why must a preempted request recompute?; chunked prefill first token (8000 tokens / 4 chunks).
1b. **`max_num_seqs` re-check (taught 11-24):** one fresh mixed case, no hint — "one heavy person (tokens) or too many people (requests)?"; then "why does lowering `max_num_seqs` make each word faster?" Page: [max-num-seqs](concepts/max-num-seqs.md).
2. **Then (≤2 concept questions, no math):** `slot_mapping` — who builds / uses it; per token vs per request. Code: [nano-vllm](raw/2026-11-24-nano-vllm.md).
3. **Main task — capacity reasoning (no hand arithmetic; the assistant does the math):** KV budget steps 2–4 — budget → max tokens → how many 4K requests fit (e.g. M1 Pro 16 GB with a small model). Learner finds the 4 fields in a real `config.json`. Use the [KV memory formula](concepts/kv-cache-memory.md).
4. **Later (main line item 4):** run vLLM locally once and check the formula against the KV cache size in the startup log.
5. **Teaching rule:** for a new concept, give the name + one sentence + where it sits in the main path **before** any picture; prefer one static picture; count from 0.

## Latest session close (2026-11-26)

- **New fact:** Scheduler vs `kv_cache_manager` right first try on a 2nd day; both quantization places named first try; KV quantization changes only `bytes`.
- **Gap:** preemption trigger missed again (thought a new request D forces a kick-out, 2nd time); did not know the requester itself is kicked out when it is last in `running`; weight quantization effect needed a hint (2nd time).
- **Next action:** give the full setup before each question; re-check preemption first, then the `block_size` vs `block_bytes` re-teach and the 11-24 list. Full log: [journal/2026-11-26.md](journal/2026-11-26.md).

Older session closes live in [journal/](journal/) — one file per day.

## Candidate Issues (not selected or claimed)

- [#40004 — priority preemption](https://github.com/vllm-project/vllm/issues/40004): excellent Scheduler relevance, but broad.
- [#64460 — admission backoff at KV saturation](https://github.com/vllm-project/vllm/issues/64460): useful Scheduler policy, CPU reasoning possible, GPU speedup unproven here.
- [#60044 — explain prefix-cache misses](https://github.com/vllm-project/vllm/issues/60044): useful KV observability; directly connected to the 2026-11-11 free-queue topic; likely needs design discussion.

Compare them using the [Issue-selection filter](ROADMAP.md#issue-selection-filter). Recheck status and ownership before work.

## Optional

- **GPU checkpoint:** if Colab provides a suitable NVIDIA GPU, run a small model and a short notebook benchmark. Record GPU type, vLLM version, model, commands and timings.
- **EAGLE quiz:** [2026-11-05](reviews/2026-11-05.md) stays saved as P2; not a prerequisite.
- **Parked investigation:** [vLLM #65161 — EAGLE + CPU KV offloading](issues/vllm-65161.md) is a historical lesson, not the active queue. Do not start a duplicate PR without a current reproducer.

## Where else to look

Knowledge inventory: [SKILL_MAP.md](SKILL_MAP.md) · formal verification: [SKILL_VALIDATION.md](SKILL_VALIDATION.md) · upstream PRs: [contributions/README.md](contributions/README.md) (single maintained list) · reported experiments: [CHAT_REPORTED_RESULTS.md](experiments/CHAT_REPORTED_RESULTS.md).

**Session close rule:** replace *Latest session close* with **one new fact, one gap, one next action**, and update *Next session*. Put the full session in `journal/YYYY-MM-DD.md`.
