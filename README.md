# Inference Infra Lab

A public learning and contribution log for **LLM inference engineering**.

**Goal:** Understand, test, and improve the core path of an inference engine. Use real vLLM Issues as practice. Do not study every interesting edge case.

**Current constraint (2026-11-04):** No personal GPU is available. A free Colab GPU may be used for short optional tests, but availability is not guaranteed. Main-path source tracing and CPU tests remain the priority. Prior single-GPU inference deployment experience is acknowledged.

![Inference Engineer knowledge map](assets/inference-skill-map.svg)

## Start here

| Question | Read |
| --- | --- |
| What should I do next? | **[CURRENT.md](CURRENT.md)** — one active learning focus and the next steps |
| What should I learn, and what can wait? | **[ROADMAP.md](ROADMAP.md)** — priorities, Issue filter, and no-GPU plan |
| What have I studied? | **[SKILL_MAP.md](SKILL_MAP.md)** — knowledge inventory |
| What have I verified myself? | **[SKILL_VALIDATION.md](SKILL_VALIDATION.md)** — L1–L4 and A/H evidence |
| How do we study and test? | [LEARNING_PROTOCOL.md](LEARNING_PROTOCOL.md) — repeatable workflow |

## Current main path

```text
Request → Scheduler → KV Cache / BlockPool → Model Runner → GPU
             ↑                  ↑                 ↑
          CPU tests          CPU tests       source / mock tests
```

**For now:** trace the path, then fix a small, real, CPU-testable core problem. **Later, when hardware is available:** add NVIDIA profiling and serving benchmarks.

**Park for now:** deep 2Q/ARC policy design, EAGLE lookahead edge cases, special accelerators, and broad multi-node work. Basic concepts remain useful.

## Evidence and history

- [Contributions](contributions/README.md) — PR links and upstream status.
- [Experiments](experiments/README.md) — what counts as proof; [chat-reported results](experiments/CHAT_REPORTED_RESULTS.md) are marked separately.
- [Review queue](REVIEW_QUEUE.md) and [next blind quiz](reviews/2026-11-05.md) — check understanding without hints.
- [Concept notes](concepts/) and [Issue notes](issues/) — detailed explanations and investigations.
- [Journal](journal/) — dated learning history, not the current task list.
- [SVG learning demo](visualizations/eagle-lookahead.md) — specialist material remains available, but does not dominate the home page.

**Evidence rule:** A passing CPU test proves only what that test covers. An open PR is not a merged contribution. A CPU microbenchmark is not a GPU performance result. Keep private customer work and data out of this repository.
