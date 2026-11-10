# Knowledge Map and Evidence

**Audit date:** 2026-11-04. **Purpose:** show the knowledge inventory and gaps, not formal exam results. See **[SKILL_VALIDATION.md](SKILL_VALIDATION.md)** for L1–L4 validation. **A** means you design and direct Agent tests; **H** means hands-on, independent validation. The plan lives in [ROADMAP.md](ROADMAP.md); the next action in [CURRENT.md](CURRENT.md).

![Inference engine knowledge map](assets/inference-skill-map.svg)

## Formal validation

**Studied does not mean verified.** [SKILL_VALIDATION.md](SKILL_VALIDATION.md) records formal L1–L4 skill checks and verification methods. **A requires a user-authored test plan and pass criteria before Agent execution**; an Agent-designed test is not automatically A. **H** requires independent reasoning and hands-on evidence. No past tests are retroactively marked as passes.

## What the statuses mean

- **Seen / guided:** discussed; recognition is not independent mastery.
- **Explained:** able to explain the idea; further review can still be needed.
- **Concept checked:** correctly answered a new conceptual question; no code proof implied.
- **Code/test evidence:** a real PR or test was described; AI-assisted tests do not prove independent design skill.
- **Measured:** only a repeatable, well-described benchmark supports this label.
- **Blocked:** requires a GPU not currently available.

## Knowledge inventory

| Main-path skill | Current evidence | Main gap | Priority |
| --- | --- | --- | --- |
| Prefill vs decode; compute vs bandwidth | Explained in earlier chats; **memory-bound decode, batching and turning point practised (guided, 2026-11-10)** — [note](concepts/memory-bound-and-batching.md) | Explain and diagnose with an independent example | P0 |
| MHA / MQA / GQA and K/V sizes | Guided explanations | Calculate KV bytes from model config | P0 |
| Weights vs hidden states | Basic concept check | Connect projections to K/V and draft states | P0 |
| PagedAttention and BlockPool | Concept studied; open draft [#64281](https://github.com/vllm-project/vllm/pull/64281) reports CPU tests | Independently trace allocation, `ref_cnt`, release | P0 |
| Prefix caching / block hashes | Explained | CPU test for hit/miss and cache limits | P0 |
| Scheduler, batching, token budgets | **Basic Token Budget explained and answered correctly in guided follow-up examples (2026-11-04)**; request-count and KV-space distinctions checked; older CPU simulations reported | Independently trace real `schedule()` control flow, allocation failures, and preemption; then verify via CPU test | P0 |
| Model Runner | Mentioned in prior code discussions | End-to-end input/state source trace | P0 |
| Performance experiments | Prior real serving comparisons reported; recent CPU experiments are chat-reported | Reproducible public experiments, clear confounders | P0 |
| Open-source testing | [#61801](https://github.com/vllm-project/vllm/pull/61801), [#64281](https://github.com/vllm-project/vllm/pull/64281), [#65366](https://github.com/vllm-project/vllm/pull/65366) exist (all open at audit) | Upstream review and independent understanding | P1 |
| Serving and deployment | Real single-NVIDIA-GPU inference deployment reported; private artifacts absent | Public-safe write-up, monitoring/K8s evidence | P1 |
| CUDA / NVIDIA profiling | No shareable profiler / kernel experiment; **no GPU available now** | Revisit when access returns | Blocked |
| LRU / 2Q / ARC | Basic eviction concepts studied | Defer algorithm internals | P2 |
| EAGLE / speculative decoding | Draft/verify explained; prefix-boundary concept checked | Defer detailed Lookahead/Offloading implementation | P2 |
| MoE, P/D disaggregation, NCCL/RDMA | Terms discussed | Defer until a target role needs them | P3 |
| nano-vLLM / SGLang / vLLM-Metal | Studied or compared as context | Do not replace vLLM main-path work | Optional |

**Priority:** P0 = main trunk; P1 = next; P2/P3 = side branches. No single numerical skill score or claim of job readiness is implied.

## Evidence sources

**GitHub-confirmed public work:** [contributions/README.md](contributions/README.md) records three personal upstream PRs and their limitations. They were **open, not merged** at this audit.

**Prior experience, not publicly reproducible here:** the learner reports having deployed a single-GPU local LLM and compared vLLM with SGLang using TTFT, TPOT and throughput. A separate professional project included PyTorch-to-ONNX comparison. Do not publish private customer details, code or measurements.

**Conversation-reported engineering experiments:** [experiments/CHAT_REPORTED_RESULTS.md](experiments/CHAT_REPORTED_RESULTS.md) includes BlockPool, admission control, preemption, GQA and cache-policy cases. These are not automatically independent, rerunnable benchmarks.

**Concept and historical evidence:** [concepts/](concepts/), [issues/](issues/), [journal/](journal/), and [reviews/](reviews/). The [#65161 EAGLE investigation](issues/vllm-65161.md) remains a reference, not the current main task.

## What matters for jobs

```text
Request → Scheduler → KV Manager / BlockPool → Model Runner → GPU
              |                 |                    |
          CPU tests         CPU tests          source / mock tests
```

**Highest active gap:** independently understand and test the Scheduler → KV → Model Runner path. **Next hardware-dependent gap:** profiling and NVIDIA performance measurement.

The [ROADMAP.md](ROADMAP.md) defines the order, Issue scoring, and learning stages. Learn the trunk before optional branches.
