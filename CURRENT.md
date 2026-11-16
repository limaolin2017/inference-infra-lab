# Current — Read This First

**Last reviewed:** 2026-11-16  
**Goal:** Build job-ready inference-core skill through focused, verifiable upstream work.  
**Hardware:** No owned or continuously available GPU. **Google Colab free GPU is an optional, availability-dependent way to run short NVIDIA tests.** Never require it for CPU core work.

## One active learning focus

**Trace one request through `Scheduler → KV Cache Manager / BlockPool → Model Runner`.**

| Stage | Status (all guided — no independent L3 trace yet) | Concept notes |
| --- | --- | --- |
| 1. Scheduler | Concepts mostly learned: token budget, `max_num_seqs`, preemption, Waiting gate | [scheduler](concepts/scheduler-and-cache-policy.md) |
| 2. KV Cache Manager / BlockPool | Concepts + **code check done 2026-11-13** (guided, learner-located) | [BlockPool life cycle](concepts/blockpool-block-life-cycle.md) · [Block Table / slot](concepts/block-table-and-slot-mapping.md) |
| 3. Model Runner | **In progress** — `input_ids` / `positions` right on 2026-11-13 (guided); `slot_mapping` concept right on 2026-11-16 (guided); chunked prefill concept only; source not yet located | [Model Runner inputs](concepts/model-runner-inputs-and-chunked-prefill.md) |
| After all 3 | Independent `schedule()` trace → one CPU check → choose a real Issue | — |

Side concept (GPU): [memory-bound decode and batching](concepts/memory-bound-and-batching.md).

## Next session

1. **Morning review (≤3 questions, fresh cases, one at a time):** top of [REVIEW_QUEUE.md](REVIEW_QUEUE.md) (2026-11-16 rows) — `max_num_batched_tokens` direction (missed 4 times; learner's own sentence first); evict hash timing (Scheduler, not GPU); `popleft_n` vs `on_block_allocated` (not yet re-checked).
2. **Main task — Model Runner source (~15 min, learner-located):** find where `positions` are built for a step (start from `prepare_attn()` / input prep in [model_runner.py](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/worker/gpu/model_runner.py)). Teach one tiny step at a time.
3. **Then:** in the same file, find where `slot_mapping` is computed (`compute_slot_mappings()` called from `prepare_attn()`), and match it to the 3-step formula learned on 2026-11-16 ([Block Table / slot](concepts/block-table-and-slot-mapping.md)).
4. **Teaching rule:** for a new concept, give the name + one sentence + where it sits in the main path **before** any picture; prefer one static picture; count from 0.

## Latest session close (2026-11-16)

- **New concept (guided):** `slot_mapping` — group = `pos // block_size`, Block Table → physical block, offset = `pos % block_size`, `slot = block × block_size + offset`; pos 2 with table `[7, 2]` → 30 (right).
- **Review fact (guided):** evict removes the hash in the **Scheduler step** (`get_new_blocks()`, right after `popleft_n()`), before GPU work; the old K/V is only overwritten later by the new owner's forward pass. Shared-block `ref_cnt` now right (3 → 2 → 1).
- **Gap:** `max_num_batched_tokens` direction flipped again (4th time) — raise = more prefill per step = faster first token; lower = lighter steps = less stutter. Free-queue "why end" reason forgotten, restated.
- **Next action:** see *Next session* above.

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
