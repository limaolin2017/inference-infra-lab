# Upstream Contributions

**Audit:** 2026-11-04. These are personal GitHub PRs, **not merged** at time of check. Reported test results come from PR descriptions; do not treat a passed test as proof of independent understanding.

| Work | State checked | Evidence and limits |
| --- | --- | --- |
| [#61801 — Fix placeholder count in prefix-caching multi-modal example](https://github.com/vllm-project/vllm/pull/61801) | Open, not merged | Documentation fix; shows care with prefix caching docs, but not inference runtime optimization |
| [#64281 — Reject negative block counts in BlockPool and free queue](https://github.com/vllm-project/vllm/pull/64281) | Open **draft**, not merged | Small core guard; PR reports 337+49 CPU unit passes and red/green regressions. No GPU/E2E serving benchmark; DCO/human review pending |
| [#65366 — Return HTTP 400 for empty DeepSeek V4.1 developer messages](https://github.com/vllm-project/vllm/pull/65366) | Open, not merged | Tokenizer/HTTP regression; PR reports 53 combined CPU passes; not a cache, Scheduler or GPU performance change |

## Reported engineering work outside these PRs

Previous conversations cover [2Q/ARC cache-hit promotion](https://github.com/vllm-project/vllm/pull/40270), preemption/admission simulations, BlockPool lookup microbenchmarks and GQA comparisons. These are **learning and experiment history**. See [CHAT_REPORTED_RESULTS](../experiments/CHAT_REPORTED_RESULTS.md); avoid claiming upstream merged wins, independently designed GPU benchmarks or full production experience.

## Why this matters for interviews

The existing PRs show an ability to navigate a large codebase and construct targeted tests with AI assistance. The **main missing evidence** for inference-specific jobs is a production-like, reproducible **GPU serving / profiling** case and an independently explained request-path trace. See [SKILL_MAP](../SKILL_MAP.md).

Track review, acceptance, test artifacts and any future merge separately. Do not inflate a contribution just because a PR is open.
