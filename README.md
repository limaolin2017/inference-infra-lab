# Inference Infra Lab

A public learning and contribution log for **LLM inference engineering**.

**Goal:** Understand, test, and improve the core path of an inference engine (vLLM). Use real vLLM Issues as practice. Do not study every interesting edge case.

**Hardware:** No personal GPU. A free Colab GPU may be used for short optional tests, but availability is not guaranteed. Main-path source tracing and CPU tests come first. Prior single-GPU inference deployment experience is acknowledged.

![Inference Engineer knowledge map](assets/inference-skill-map.svg)

## Start here

| Question | Read |
| --- | --- |
| What should I do next? | **[CURRENT.md](CURRENT.md)**: the active focus, next session, latest session close |
| What is due for review? | **[REVIEW_QUEUE.md](REVIEW_QUEUE.md)**: confusions to retest, newest first |
| What should I learn, and what can wait? | [ROADMAP.md](ROADMAP.md): priorities, Issue filter, no-GPU plan |
| What have I studied? | [SKILL_MAP.md](SKILL_MAP.md): knowledge inventory |
| What have I verified myself? | [SKILL_VALIDATION.md](SKILL_VALIDATION.md): L1–L4 and A/H evidence |
| How do we study and test? | [LEARNING_PROTOCOL.md](LEARNING_PROTOCOL.md): session format and workflow |

## Main path and progress

```text
Request → Scheduler → KV Cache Manager / BlockPool → Model Runner → GPU
             ↑                    ↑                        ↑
          CPU tests            CPU tests            source / mock tests
```

| Stage | Status | Concept notes |
| --- | --- | --- |
| Scheduler | Concepts mostly learned (guided) | [Scheduler and cache policy](concepts/scheduler-and-cache-policy.md) |
| KV Cache Manager / BlockPool | **In progress** | [BlockPool block life cycle](concepts/blockpool-block-life-cycle.md) · [Block Table and slot mapping](concepts/block-table-and-slot-mapping.md) · [Model states and KV cache](concepts/model-states-and-kv-cache.md) |
| Model Runner | Started (concepts) | [Model Runner inputs and chunked prefill](concepts/model-runner-inputs-and-chunked-prefill.md) |
| GPU execution | Concept only | [Memory-bound decode and batching](concepts/memory-bound-and-batching.md) |

"Guided" means learned with explanation in a session. It is not yet an independent source trace or test. See [SKILL_VALIDATION.md](SKILL_VALIDATION.md).

**Now:** trace the path, then fix a small, real, CPU-testable core problem. **Later, when hardware is available:** add NVIDIA profiling and serving benchmarks.

**Parked for now (P2):** deep 2Q/ARC policy design, EAGLE lookahead edge cases ([note](concepts/speculative-decoding-eagle.md)), special accelerators, and broad multi-node work.

## Evidence and history

- [Contributions](contributions/README.md): PR links and upstream status.
- [Experiments](experiments/README.md): what counts as proof. [Chat-reported results](experiments/CHAT_REPORTED_RESULTS.md) are marked separately.
- [Daily learning log](journal/): one file per session day, with every question, answer and grade.
- [Issue notes](issues/): investigations, such as [#65161](issues/vllm-65161.md) (parked).
- [Reviews](reviews/): the optional [EAGLE blind quiz](reviews/2026-11-05.md) and archived review rows.

**Evidence rule:** A passing CPU test proves only what that test covers. An open PR is not a merged contribution. A CPU microbenchmark is not a GPU performance result. Keep private customer work and data out of this repository.
