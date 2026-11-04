# Roadmap — Job-Ready Inference Engineer

**Updated: 2026-11-04.** Work backward from jobs, not forward from a random issue. See the [animated architecture / knowledge map](SKILL_MAP.md) and [current evidence](CURRENT.md).

![Inference Engineer skill map](assets/inference-skill-map.svg)

## The essential trunk: P0

| Order | Skill to demonstrate | Concrete pass test | Current evidence |
| --- | --- | --- | --- |
| 1 | Basic Transformer/attention, prefill vs decode | Derive K/V size and explain compute-vs-bandwidth with one real model | Explained; independent technical check pending |
| 2 | vLLM request lifecycle: API, Scheduler, KV manager, Model Runner | Trace one prefill and one decode request in current source | Pieces discussed; full trace missing |
| 3 | KV blocks, prefix cache, scheduling, admission/preemption | Reproduce hit/miss, KV shortage, and a scheduler action | Guided concepts; some chat-reported CPU simulations and an open draft core PR |
| 4 | Reproducible NVIDIA inference benchmark | Re-run a public model; publish raw TTFT, TPOT, throughput, GPU memory over varied load | **Prior real deployment and vLLM/SGLang comparison reported; public artifacts missing** |
| 5 | Profiling and reasoning | Explain a measured slowdown and an A/B comparison; include caveats | **Public GPU kernel/CPU profiler trace missing** |
| 6 | Tests, CI, reproducibility | Write regression tests and show commands and failure-before/pass-after | Some reported CPU PR tests; core performance case not yet complete |

**P0 completion is not reading a chapter:** explain independently + source trace + test/measurement artifact. Scores are skill-specific and do not prove overall job readiness.

## Choose a branch after the trunk

### Track A — Serving / platform (e.g., [Luma Remote EU](https://jobs.ashbyhq.com/lumaai/d0f68f86-1ec2-4443-a730-0d8f68842545))

Learn **Linux, Docker, Kubernetes, queues, deployment, observability, reliability and GPU fleet cost**. Build a small deployed inference service and load-test it. These employers expect production systems, not just vLLM issue familiarity.

### Track B — Inference optimization (e.g., [Featherless Remote World](https://jobs.ashbyhq.com/featherlessai/1762b15b-9c0b-4a20-a2b3-ed12c8222156/))

Learn **GPU profiling, CUDA/Triton basics, memory bandwidth, batching, attention/quantization trade-offs**. Build a baseline → profiling → minimal improvement → benchmark case study. This is the deeper GPU track.

## Defer the side quests (P2/P3)

- Full 2Q/ARC implementation or formal policy analysis — know the LRU/2Q/ARC idea, skip full algorithms unless profiling shows eviction is the bottleneck.
- EAGLE lookahead hash, connector corner cases — know how draft/target verification works, then park #65161 if merged fixes remove the original symptom.
- Advanced MoE, NCCL/RDMA, multi-node serving, NPU/Huawei and unusual hardware — do later if a target role explicitly requires them.
- Small unrelated parser/API PRs — useful for OSS practice, but not evidence that can substitute for a GPU inference case.

## Current 8-week sequence — no GPU available

**GPU access = zero as of 2026-11-04.** Do not make NVIDIA rental a hidden prerequisite. The goal remains GPU inference engineering, but progress now comes from core-code learning and CPU verification.

| Window | Most valuable deliverable | Pass condition |
| --- | --- | --- |
| Weeks 1–2 | Request-path source trace: Scheduler → KV manager → Model Runner | Independently explain one prefill/decode state transition with source links and a small CPU test |
| Weeks 3–4 | One bounded Scheduler or BlockPool bug | Failing CPU reproducer, smallest fix, passing regression tests; explain invariants |
| Weeks 5–6 | Second main-path task, preferably scheduling observability or prefix-cache correctness | Reproducible CPU tests, clear failure cases, compare current source/PRs |
| Weeks 7–8 | One portfolio case study of real upstream work | Explain problem, code, tests, trade-offs, GPU-validation limits, and review outcome |

**When GPU access returns:** add a public-safe serving workload and measure TTFT, TPOT, throughput, and profiler traces. Do not claim those results before then.

**Existing real-world experience:** the learner reports having deployed and delivered a single-NVIDIA-GPU local LLM service, chosen between vLLM and SGLang using TTFT/TPOT/throughput, and separately benchmarked a PyTorch-to-ONNX conversion. Private customer details and results are not published here. The immediate gap is safe, inspectable evidence and engine-level profiling, **not** learning to launch a model from scratch.

This is a learning plan, not a guaranteed hiring timeline. Adjust based on actual workload and access to GPU resources.

## Issue admission filter

**An Issue is a practice problem, not the syllabus.** Prefer the main path (Scheduler / KV Cache / BlockPool / Model Runner) and aim for **70% hands-on tests/source tracing, 30% just-in-time concepts**.

Select candidate Issues using four explicit questions:

1. **Role relevance (0–5):** Does it teach or prove core inference engineering?
2. **CPU reproducibility (0–5):** Can it have a meaningful failing/passing test without a GPU?
3. **New trunk skill (0–5):** Will it close a gap we actually need for a job?
4. **Time/complexity (0–5):** How much deep, unrelated research will it consume?

For comparison only, an optional heuristic is `4 × relevance + 3 × CPU feasibility + 2 × skill gain − 2 × complexity`. **This is a prioritization aid, not an objective metric or a job-readiness score.**

**Gate:** Look for an open upstream problem, no overlapping fix, small test surface, clear invariant, and a stop condition. Timebox investigation to one session, then proceed, narrow scope, or park it. CPU unit tests can prove code correctness; they do **not** prove GPU performance changes.

Current examples to review (not claimed / not yet selected):
- [#40004](https://github.com/vllm-project/vllm/issues/40004) — priority preemption; central Scheduler skill, but broad semantics and more difficult implementation.
- [#64460](https://github.com/vllm-project/vllm/issues/64460) — KV-saturation admission; central Scheduler skill, feasible CPU policy study, but hardware TPOT claim is untestable now.
- [#60044](https://github.com/vllm-project/vllm/issues/60044) — prefix-cache miss attribution; useful cache observability, may need design agreement and careful semantics.

**Depth limit:** LRU = know the rule and basic tests; 2Q/ARC = understand motivation, no deep policy implementation for now; EAGLE = know draft/verify, do not expand lookahead/offload corner cases unless a core issue needs them.

## Audit sources

- [Learning inventory, maturity and evidence gaps](SKILL_MAP.md)
- [Chat-reported tests vs. reproducible artifacts](experiments/CHAT_REPORTED_RESULTS.md)
- [Current knowledge levels and tomorrow's quiz](CURRENT.md)
- [Open personal PRs](contributions/README.md)
