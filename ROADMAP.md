# Roadmap — LLM Inference Systems

Goal: move from using inference frameworks to **reading, testing, modifying, and measuring their internals**.

This is a skill roadmap, **not** a promise about dates or job readiness.

## Track A — Core engine (highest current priority)

| Skill | Practical proof | Status |
| --- | --- | --- |
| Prefill vs. decode | Explain latency and compute/memory trade-offs | Explained, revisit |
| Attention and GQA | Trace K/V dimensions and memory cost | Studied earlier; not verified here |
| PagedAttention / BlockPool | Trace allocation, reference counts, and eviction | Studied earlier; code proof pending |
| Prefix caching | Reproduce cache hits and invalidation | Explained |
| Scheduler | Follow one waiting request through admission and preemption | Studied earlier; trace pending |
| KV offloading | Trace GPU/CPU lookup and async load | Current gap |
| Speculative decoding / EAGLE | Explain draft verification and draft-KV boundary | Current topic |

## Track B — Performance engineering

- Reproduce with controlled model, hardware, versions, and inputs.
- Measure TTFT, inter-token latency, throughput, GPU memory, and cache-hit rate.
- Separate **correctness tests** from **performance claims**.
- Compare load, recompute, and scheduling strategies under varying request lengths.
- Report regressions and failure cases.

## Track C — GPU / ML systems

- PyTorch execution and profiling.
- CUDA basics: threads, memory hierarchy, synchronization.
- Kernel profiling and bottleneck identification.
- Batching, kernel launches, memory bandwidth, and occupancy.
- Distributed inference and serving trade-offs.

## Evidence ladder

1. Explain a concept in your own words.
2. Trace the actual code path.
3. Write a regression test.
4. Make a minimal change and pass tests.
5. Measure the effect on a controlled workload.
6. Submit a clear upstream PR, if a real gap remains.

## Portfolio quality bar

An issue note is valuable only when it distinguishes:
**reported behavior**, **observed evidence**, **hypothesis**, and **result**.

A merged PR is strong evidence, but an independently designed test and a careful negative result also count as engineering work.
