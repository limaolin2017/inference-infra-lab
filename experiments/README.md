# Experiments

No benchmark artifacts have been added yet.

Do not describe a proposed test as a completed test.

For every experiment, create a folder or document with:

1. **Question** — what behavior is being tested?
2. **Environment** — vLLM commit, model/draft, GPU/CPU, relevant flags.
3. **Inputs** — workload size, prompts, concurrency, seeds.
4. **Baseline** — unmodified behavior.
5. **Variant** — one change at a time.
6. **Metrics** — TTFT (median/p95), output speed, cache hits, CPU loads, memory.
7. **Results** — raw data and command lines.
8. **Limits** — what the results cannot prove.

Use separate tests for correctness and performance.

For current priority, see [vLLM #65161](../issues/vllm-65161.md).
