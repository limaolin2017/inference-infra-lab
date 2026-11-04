# Experiments — Evidence, Not Claims

This folder is for **reproducible results**. No raw, independently rerunnable benchmark dataset has been published here yet.

Earlier conversation results are kept in [CHAT_REPORTED_RESULTS.md](CHAT_REPORTED_RESULTS.md). They are **reported observations**, not verified GPU wins.

## Checklist for any new experiment

1. Question, expected invariant, and hypothesis.
2. Environment: exact vLLM commit, hardware, model, flags, dependencies.
3. Inputs: workload, concurrency, seeds, data generation.
4. Baseline and one controlled change.
5. Commands, raw results, repeats and correctness checks.
6. Limits: what does this *not* establish?

**CPU-only** tests can prove control logic, cache state and regressions. They cannot establish NVIDIA speedups. The current learning plan is [ROADMAP.md](../ROADMAP.md).
