# Current — Read This First

**Last reviewed:** 2026-11-04  
**Goal:** Build job-ready inference-core skill through focused, verifiable upstream work.  
**Hardware:** No owned or continuously available GPU. **Google Colab free GPU is an optional, availability-dependent way to run short NVIDIA tests.** Never require it for CPU core work.

## One active learning focus

**Trace one request through `Scheduler → KV Cache Manager / BlockPool → Model Runner`.**

We have studied Scheduler and KV-cache concepts separately. The missing proof is an **independent end-to-end code explanation** and a targeted CPU test.

### Next session

1. Start with the short [2026-11-05 blind review](reviews/2026-11-05.md). Do not show answers before the attempt.
2. Trace a prefill/decode request across the three core modules. Record exact source links and where CPU-only tests are possible.
3. Check current code and overlapping PRs for a **bounded** open core Issue. Write the expected invariant and a failing CPU test before suggesting a fix.

**Optional GPU checkpoint:** If Colab provides a suitable NVIDIA GPU, run a small model and a short notebook benchmark. Record GPU type, vLLM version, model, commands and timings. Do not rely on a persistent server or assume the GPU will be available next time.

**Candidate Issues (not selected or claimed):**
- [#40004 — priority preemption](https://github.com/vllm-project/vllm/issues/40004): excellent Scheduler relevance, but broad.
- [#64460 — admission backoff at KV saturation](https://github.com/vllm-project/vllm/issues/64460): useful Scheduler policy, CPU reasoning possible, GPU speedup unproven here.
- [#60044 — explain prefix-cache misses](https://github.com/vllm-project/vllm/issues/60044): useful KV observability, likely needs design discussion.

Compare them using the [Issue-selection filter](ROADMAP.md#issue-selection-filter). Recheck status and ownership before work.

## Skills snapshot

| Area | Evidence now | Next missing proof |
| --- | --- | --- |
| Prefill / decode; Attention / GQA | Concepts studied | Explain with fresh examples and derive KV memory |
| Scheduler / admission / preemption | Guided explanations and chat-reported CPU simulations | Independent source trace + focused CPU regression |
| KV Cache / BlockPool / prefix cache | Concepts studied; [#64281](https://github.com/vllm-project/vllm/pull/64281) reports CPU tests (open draft) | Trace full block life cycle and rerun tests |
| Model Runner | Pieces discussed | Follow real input/state preparation; CPU-mock test where possible |
| NVIDIA performance / CUDA | Prior professional serving work reported; no public profiler artifacts | Optional short Colab GPU run when available; benchmark evidence still pending |
| EAGLE / Lookahead / 2Q / ARC | Basic concepts; some guided checks | **Park deeper study** unless needed for core work |

Full inventory and evidence levels: [SKILL_MAP.md](SKILL_MAP.md). Reported experiments: [CHAT_REPORTED_RESULTS.md](experiments/CHAT_REPORTED_RESULTS.md).

## Contribution status (checked 2026-11-04)

- [#61801](https://github.com/vllm-project/vllm/pull/61801): open docs PR.
- [#64281](https://github.com/vllm-project/vllm/pull/64281): open **draft**, BlockPool guards.
- [#65366](https://github.com/vllm-project/vllm/pull/65366): open, DeepSeek V4.1 error handling.

None were confirmed merged at the last check. See [contributions](contributions/README.md) for details.

## Parked investigation

[vLLM #65161 — EAGLE + CPU KV offloading](issues/vllm-65161.md) is a valuable historical lesson, **not** the active work queue. It may already be affected by merged upstream fixes. Do not start a duplicate PR without a current reproducer.

**Session close:** update this file with **one new verified fact, one gap, and one next action**. Keep historical experiments in their own files.
