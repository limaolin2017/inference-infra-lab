# Current — Read This First

**Last reviewed:** 2026-11-13  
**Goal:** Build job-ready inference-core skill through focused, verifiable upstream work.  
**Hardware:** No owned or continuously available GPU. **Google Colab free GPU is an optional, availability-dependent way to run short NVIDIA tests.** Never require it for CPU core work.

## One active learning focus

**Trace one request through `Scheduler → KV Cache Manager / BlockPool → Model Runner`.**

| Stage | Status (all guided — no independent L3 trace yet) | Concept notes |
| --- | --- | --- |
| 1. Scheduler | Concepts mostly learned: token budget, `max_num_seqs`, preemption, Waiting gate | [scheduler](concepts/scheduler-and-cache-policy.md) |
| 2. KV Cache Manager / BlockPool | **In progress** — block life cycle learned 2026-11-11; code check pending | [BlockPool life cycle](concepts/blockpool-block-life-cycle.md) · [Block Table / slot](concepts/block-table-and-slot-mapping.md) |
| 3. Model Runner | Started — flattened inputs, chunked prefill (concept only) | [Model Runner inputs](concepts/model-runner-inputs-and-chunked-prefill.md) |
| After all 3 | Independent `schedule()` trace → one CPU check → choose a real Issue | — |

Side concept (GPU): [memory-bound decode and batching](concepts/memory-bound-and-batching.md).

## Next session

1. **Morning review (≤3 questions, fresh cases, one at a time):** due items at the top of [REVIEW_QUEUE.md](REVIEW_QUEUE.md) — shared-block `ref_cnt` (N → N−1), `max_num_batched_tokens` direction (independent), Waiting gate + FCFS head-of-line mixed case.
2. **Main task — BlockPool code check (~15 min, learner-located):** in [`get_new_blocks()`](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/core/block_pool.py#L681-L722), [`touch()`](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/core/block_pool.py#L774-L792) and [`free_blocks()`](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/core/block_pool.py#L798-L839), find the line that changes `ref_cnt` and the free-queue action. Bonus: what `_maybe_evict_cached_block()` does to an old hash.
3. **Then:** `SchedulerOutput` → Model Runner — learner locates where `input_ids` / `positions` are built.

## Latest session close (2026-11-13)

- **New fact (guided; source assistant-shown):** a preempted request goes back to the Waiting **front** (`prepend_request`); with FCFS, if the Waiting head can't get blocks, the loop `break`s, so requests behind it wait too. Review: Waiting-gate reason, preempt fields and front/end were all right (guided); front/end "why" explained in own words.
- **Gap:** shared block — said `ref_cnt` goes to 0 when one of two users is preempted (correct: 2 → 1).
- **Next action:** see *Next session* above (BlockPool code check still not done).

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
