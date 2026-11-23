# Current — Read This First

**Last reviewed:** 2026-11-23 (project chat, guided)  
**Goal:** Build job-ready inference-core skill through focused, verifiable upstream work.  
**Hardware:** No owned or continuously available GPU. **Google Colab free GPU is an optional, availability-dependent way to run short NVIDIA tests.** Never require it for CPU core work.

## Main line (set 2026-11-17)

1. **KV cache memory calculation and capacity planning** (KV Cache Handbook pp. 7–9) ← **current, just started** · [KV cache memory](concepts/kv-cache-memory.md)
2. Prefill / decode performance and bottleneck analysis
3. Scheduler batching and preemption logic
4. Benchmark and profiling

The request-path trace below is the background for items 1 and 3.

## Status board — mastered / gap / unclear (updated 2026-11-23)

Update this board at every session close. Copy kept in project memory.

| Area | Mastered (can do) | Gap (cannot do alone yet) | Unclear (still confused) |
| --- | --- | --- | --- |
| KV memory (item 1) | Formula `2 × layers × KV heads × head_dim × bytes`; MHA / GQA / MQA = KV head count | Capacity math for a real model config | — (just learned) |
| `slot_mapping` | 3-step rule; pos 12 needs a new block | Fresh cases without help | offset = remainder (missed 2×); use the physical block; pos from 0; `slot_mapping` vs `block_table` |
| BlockPool | `ref_cnt`, free queue front / end, evict vs preempt (guided) | Independent code trace | Free-queue "why end" reason; evict hash timing |
| Scheduler | Token budget, preemption, Waiting gate (guided); 3 parts `waiting` / `running` / `kv_cache_manager` re-learned 11-23 (guided); preempted → front of `waiting` | Independent `schedule()` trace + CPU test; name the 3 parts alone next day; `add_request` vs `prepend_request` names | `max_num_batched_tokens` direction (missed 4×); Scheduler vs `kv_cache_manager` roles (mixed up 11-23); what triggers preemption; which request is preempted |
| Model Runner | `input_ids` / `positions` (guided) | Find `positions` / `slot_mapping` in source alone | Chunked prefill details |
| Prefill / decode (item 2) | Memory-bound decode, batching (guided) | Say alone what limits TTFT vs TPOT | — |
| Benchmark / profiling (item 4) | — | Public, rerunnable benchmark; profiling | Not started |

**Formal passes:** 0. All answers so far are guided.

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

1. **Review first (2026-11-24, from [REVIEW_QUEUE.md](REVIEW_QUEUE.md) top):** name the 3 Scheduler parts alone; Scheduler vs `kv_cache_manager` (who decides, who only answers); what triggers preemption (new C can't fit vs running A needs a block); which request is preempted and what happens to it; `add_request` vs `prepend_request`.
2. **Then (≤3 questions, fresh cases):** blocks / `slot_mapping` (offset = remainder, physical block, pos from 0).
3. **Main task — paper exercise (no local run):** M1 Pro 16 GB with a small model — how many 4K-token requests fit at once? Use the [KV memory formula](concepts/kv-cache-memory.md).
4. **Later (main line item 4):** run vLLM locally once and check the formula against the KV cache size in the startup log.
5. **Teaching rule:** for a new concept, give the name + one sentence + where it sits in the main path **before** any picture; prefer one static picture; count from 0.

## Latest session close (2026-11-23, guided)

- **New fact:** the Scheduler decides; `kv_cache_manager` only gives out / takes back KV blocks. Preemption is triggered only by a running request that needs a block when none are free — a new request just waits.
- **Gap:** mixed up Scheduler and `kv_cache_manager`; did not know `prepend_request()`; not yet asked which request gets preempted.
- **Next action:** see *Next session* above. Full log: [journal/2026-11-23.md](journal/2026-11-23.md).

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
