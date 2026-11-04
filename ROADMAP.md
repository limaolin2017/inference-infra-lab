# Roadmap — Job-Ready Inference Engineer

This is the **plan**, not a log of everything studied. For current status use [CURRENT.md](CURRENT.md); for a full knowledge audit use [SKILL_MAP.md](SKILL_MAP.md).

## Learn the main path first

| Order | Priority | Skill | Proof to build |
| --- | --- | --- | --- |
| 1 | P0 | Prefill/decode, Attention, GQA | Explain the trade-off; derive approximate KV bytes |
| 2 | P0 | Scheduler + KV Manager + BlockPool | Follow a request, reproduce a cache/scheduling case on CPU |
| 3 | P0 | Model Runner state / input path | Trace Scheduler output into runner state; test suitable interfaces |
| 4 | P0 | Debugging, tests, CI, code reviews | Failing test → small fix → passing tests → explain limits |
| 5 | P1 (blocked by hardware) | NVIDIA profiling and inference benchmarks | Reproducible TTFT, TPOT, throughput, memory, and profiler traces |
| 6 | P1 | Serving/observability | Logs, queues, metrics, deployment and failure handling |

**No GPU available (2026-11-04).** Items 1–4 can progress now. Do not pretend CPU evidence proves GPU speedups. Previous real single-GPU deployment is experience, not current hardware access.

## Issue-selection filter

**Issues are practice problems, not the syllabus.** Before choosing one, assess:

| Question | Scale |
| --- | --- |
| Does it build a job-relevant core skill? | 0–5 (higher = better) |
| Can the behavior be checked with a CPU test? | 0–5 (higher = better) |
| Does it close a missing main-path skill? | 0–5 (higher = better) |
| How deep and costly is the required investigation? | 0–5 (higher = worse) |

Optional comparison: `4 × relevance + 3 × CPU testability + 2 × skill gain − 2 × cost`. **This is only a heuristic.** It is not a measured job-readiness score.

**Go** only if: the issue is still real on current main; there is no duplicate fix; the invariant is clear; a test is practical; the scope fits roughly 1–2 focused sessions. **Park** broad research or GPU-only claims.

Examples worth checking, **not yet assigned**:
- [#40004 — high-priority waiting requests](https://github.com/vllm-project/vllm/issues/40004): strong Scheduler topic; potentially large change.
- [#64460 — KV saturation / admission](https://github.com/vllm-project/vllm/issues/64460): strong Scheduler topic; cannot verify GPU TPOT gain now.
- [#60044 — prefix-cache miss attribution](https://github.com/vllm-project/vllm/issues/60044): useful observability; design agreement may be needed.

## No-GPU learning sequence (adjust as needed)

| Stage | Deliverable |
| --- | --- |
| 1. Understand | One independent request-path walkthrough: Scheduler → KV → Model Runner |
| 2. Verify | One bounded, CPU-reproducible Scheduler / BlockPool regression |
| 3. Extend | A second core issue or a focused observability test, after checking overlap |
| 4. Present | One readable case study with root cause, code, tests, limits, and upstream review |

When hardware becomes available, add a **public-safe NVIDIA** benchmark and profiling case. Hardware is a future requirement for GPU performance work, **not a blocker** for current code contributions.

## Study less deeply, on purpose

| Learn the core idea | Defer for now |
| --- | --- |
| LRU, prefix-cache reuse, basic eviction | Full 2Q/ARC implementation or research comparison |
| Draft/target speculative decoding | EAGLE lookahead and connector corner cases |
| What offloading and TP mean | RDMA/NCCL, large multi-node designs, exotic accelerators |
| Basic attention and GPU memory costs | CUDA kernel optimization before we can profile on NVIDIA |

A job can ask for deeper skill later. Reopen a branch only when a role or main-path test requires it.

## Two directions after the shared core

- **Serving/platform:** Docker, Linux, Kubernetes, queues, autoscaling, monitoring and reliability.
- **Inference optimization:** GPU profiling, batching, memory bandwidth, kernel work, CUDA/Triton.

The immediate goal is **one strong, independently explained core contribution**, not the number of open PRs. Use the [learning protocol](LEARNING_PROTOCOL.md) and keep evidence separate from claims.
