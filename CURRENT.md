# Current — Read This First

**Last reviewed:** 2026-11-10  
**Goal:** Build job-ready inference-core skill through focused, verifiable upstream work.  
**Hardware:** No owned or continuously available GPU. **Google Colab free GPU is an optional, availability-dependent way to run short NVIDIA tests.** Never require it for CPU core work.

## One active learning focus

**Trace one request through `Scheduler → KV Cache Manager / BlockPool → Model Runner`.**

**New concept checkpoint (2026-11-04):** Token Budget basics understood. The learner answered budget subtraction and split-allocation cases correctly and distinguished token budget, `max_num_seqs`, and KV space. This is a guided session checkpoint, **not a formal H-confirmed pass**. See [Scheduler concept note](concepts/scheduler-and-cache-policy.md) for examples.

**Still missing:** an independent end-to-end `schedule()` source trace and one focused CPU check. Do not repeat basic budget arithmetic unless it causes a real mistake.

### Next session

1. **Main task:** trace `Scheduler.schedule()` using a small scenario: how Running requests consume token budget, how remaining budget reaches Waiting requests, and when KV allocation blocks progress. Then follow the selected work into KV Manager / BlockPool → Model Runner.
2. **Small proof:** predict one changed input/queue case, then verify it with an available CPU unit test or a clear source-level check. Record what did and did not pass.
3. **Real Issue:** inspect current source, existing tests and overlapping PRs for one bounded Scheduler/KV/Model Runner problem. Only then decide whether a fix is worth a PR.

**Optional review:** [2026-11-05 EAGLE quiz](reviews/2026-11-05.md) remains saved for later, but it is **not a prerequisite** to the main-path task. Record formal verification in [SKILL_VALIDATION.md](SKILL_VALIDATION.md) only when actual evidence exists.

**Optional GPU checkpoint:** If Colab provides a suitable NVIDIA GPU, run a small model and a short notebook benchmark. Record GPU type, vLLM version, model, commands and timings. Do not rely on a persistent server or assume the GPU will be available next time.

**Candidate Issues (not selected or claimed):**
- [#40004 — priority preemption](https://github.com/vllm-project/vllm/issues/40004): excellent Scheduler relevance, but broad.
- [#64460 — admission backoff at KV saturation](https://github.com/vllm-project/vllm/issues/64460): useful Scheduler policy, CPU reasoning possible, GPU speedup unproven here.
- [#60044 — explain prefix-cache misses](https://github.com/vllm-project/vllm/issues/60044): useful KV observability, likely needs design discussion.

Compare them using the [Issue-selection filter](ROADMAP.md#issue-selection-filter). Recheck status and ownership before work.

**Session close (2026-11-09):**

- **New fact (assistant-shown, not an independent L3 trace):** `ModelRunner.prepare_attn()` → `BlockTables.compute_slot_mappings()` computes `slot_id = block_table[req][position // block_size] * block_size + position % block_size`. See [Block Table and slot mapping](concepts/block-table-and-slot-mapping.md).
- **Gap:** no independent source trace yet; "who allocates blocks vs who computes the slot" needs a blind retest ([REVIEW_QUEUE.md](REVIEW_QUEUE.md)).
- **Progress:** `schedule()` skeleton seen with assistant-shown line numbers; one prediction (WAITING skipped after a preemption) was Partial.
- **Done today (concepts, guided):** `schedule()` skeleton; `allocate_slots()` new-block count; preemption frees a whole request and resets progress; block life cycle (`ref_cnt`, free queue, reuse vs evict). First learner-located source line (`_preempt_request()`).
- **Next action:** (1) ~15-minute code check for the KV Manager / BlockPool stage: learner finds where `ref_cnt` goes up, goes down, and where a block returns to the free queue. (2) Then the next stage: `SchedulerOutput` → Model Runner.

**Session close (2026-11-10):**

- **New fact (concept, guided):** decode is memory-bound — each step reads all weights + each request's KV cache from HBM; batching shares the weight read, not the KV read. See [memory-bound note](concepts/memory-bound-and-batching.md).
- **Gap:** throughput vs per-user TPOT were mixed up; memory path first placed at CPU → GPU ([REVIEW_QUEUE.md](REVIEW_QUEUE.md)). The 2026-11-09 P0 reviews were **not** done and are still due.
- **Next action:** unchanged from 2026-11-09 — KV Manager / BlockPool code check, then `SchedulerOutput` → Model Runner. Review done later on 2026-11-10 (3 questions): WAITING gate after preemption missed a 3rd time; preempt field confused (`num_computed_tokens`, not `ref_cnt`). Next review: those two first.

## Skills snapshot

| Area | Evidence now | Next missing proof |
| --- | --- | --- |
| Prefill / decode; Attention / GQA | Concepts studied | Explain with fresh examples and derive KV memory |
| Scheduler / admission / preemption | Guided explanations and chat-reported CPU simulations | Independent source trace + focused CPU regression |
| KV Cache / BlockPool / prefix cache | Concepts studied; [#64281](https://github.com/vllm-project/vllm/pull/64281) reports CPU tests (open draft) | Trace full block life cycle and rerun tests |
| Model Runner | Pieces discussed | Follow real input/state preparation; CPU-mock test where possible |
| NVIDIA performance / CUDA | Prior professional serving work reported; no public profiler artifacts | Optional short Colab GPU run when available; benchmark evidence still pending |
| EAGLE / Lookahead / 2Q / ARC | Basic concepts; some guided checks | **Park deeper study** unless needed for core work |

Knowledge inventory: [SKILL_MAP.md](SKILL_MAP.md). **Formal verification (L1–L4, A/H): [SKILL_VALIDATION.md](SKILL_VALIDATION.md).** Reported experiments: [CHAT_REPORTED_RESULTS.md](experiments/CHAT_REPORTED_RESULTS.md).

## Contribution status

See [contributions/README.md](contributions/README.md) for the **single maintained list** of upstream PRs, reported tests, and last-checked statuses. Do not copy status lists into this file.

## Parked investigation

[vLLM #65161 — EAGLE + CPU KV offloading](issues/vllm-65161.md) is a valuable historical lesson, **not** the active work queue. It may already be affected by merged upstream fixes. Do not start a duplicate PR without a current reproducer.

**Session close:** update this file with **one new verified fact, one gap, and one next action**. Keep historical experiments in their own files.
