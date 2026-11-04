# Roadmap — Job-Ready Inference Engineer

This is the **plan**, not a log of everything studied. For current status use [CURRENT.md](CURRENT.md); for a full knowledge audit use [SKILL_MAP.md](SKILL_MAP.md).

## Learn the main path first

| Order | Priority | Skill | Proof to build |
| --- | --- | --- | --- |
| 1 | P0 | Prefill/decode, Attention, GQA | Explain the trade-off; derive approximate KV bytes |
| 2 | P0 | Scheduler + KV Manager + BlockPool | Follow a request, reproduce a cache/scheduling case on CPU |
| 3 | P0 | Model Runner state / input path | Trace Scheduler output into runner state; test suitable interfaces |
| 4 | P0 | Debugging, tests, CI, code reviews | Failing test → small fix → passing tests → explain limits |
| 5 | P1 (opportunistic Colab) | NVIDIA profiling and inference benchmarks | Short in-notebook benchmark when a suitable Colab GPU is assigned; otherwise defer |
| 6 | P1 | Serving/observability | Logs, queues, metrics, deployment and failure handling |

**No personal GPU available (2026-11-04).** A free Google Colab runtime may occasionally provide an NVIDIA GPU, but availability, models and session length are not guaranteed. Items 1–4 can progress without GPU. Previous real single-GPU deployment is experience, not current hardware access.

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

Live examples under consideration are listed only in [CURRENT.md](CURRENT.md). Recheck their upstream status, overlapping PRs, and CPU-test scope before starting.

## CPU-first learning sequence (adjust as needed)

| Stage | Deliverable |
| --- | --- |
| 1. Understand | One independent request-path walkthrough: Scheduler → KV → Model Runner |
| 2. Verify | One bounded, CPU-reproducible Scheduler / BlockPool regression |
| 3. Extend | A second core issue or a focused observability test, after checking overlap |
| 4. Present | One readable case study with root cause, code, tests, limits, and upstream review |

**Colab option:** Use a notebook GPU for short, public-safe vLLM correctness tests or small TTFT/TPOT/throughput measurements when assigned. Inspect `nvidia-smi`, available VRAM, CUDA/Python compatibility and model size first. Keep setup and results in the notebook. Free Colab is not suitable as a persistent inference service. Follow [Google Colab's FAQ](https://research.google.com/colaboratory/faq.html): resources are not guaranteed, sessions are limited, and bypassing the notebook UI/remote control is restricted. Do not claim representative production performance from a single short shared GPU run.

GPU access is **optional for the current CPU-first core contributions**, not a blocker. Add stronger public-safe NVIDIA benchmarks when stable hardware access becomes possible.

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
