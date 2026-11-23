# Index — every page in this wiki

Read this first to find a page. Updated on every change. Rules: [CLAUDE.md](CLAUDE.md). Recent activity: [log.md](log.md).

## Learner state

| Page | Summary |
| --- | --- |
| [CURRENT.md](CURRENT.md) | Main line, status board (mastered / gap / unclear), next session |
| [REVIEW_QUEUE.md](REVIEW_QUEUE.md) | Confusions to retest, newest first |
| [QUESTIONS.md](QUESTIONS.md) | Every learner question by topic — shows unclear areas |
| [MISTAKES.md](MISTAKES.md) | What was wrong and how many times; weak points by pattern |
| [SKILL_VALIDATION.md](SKILL_VALIDATION.md) | Formal L1–L4 and A/H passes (0 so far) |
| [SKILL_MAP.md](SKILL_MAP.md) | Knowledge inventory and gaps |
| [journal/](journal/) | Full log of each session, one file per day |

## Plan and method

| Page | Summary |
| --- | --- |
| [ROADMAP.md](ROADMAP.md) | Priorities, trunk-first rule, Issue-selection filter, no-GPU plan |
| [LEARNING_PROTOCOL.md](LEARNING_PROTOCOL.md) | Session format, review split, evidence rules |

## Components (vLLM parts: owns / decides / answers / called by)

| Page | Summary |
| --- | --- |
| [components/README](components/README.md) | Who calls whom; "who decides?" table |
| [scheduler](components/scheduler.md) | Owns `waiting` / `running`; the only part that decides |
| [kv-cache-manager](components/kv-cache-manager.md) | Front desk: blocks or `None`; decides nothing |
| [block-pool](components/block-pool.md) | Warehouse: physical blocks, free queue, hash |
| [model-runner](components/model-runner.md) | Kitchen: builds inputs, runs the GPU step |

## Concepts (by main-path stage)

Main path: Request → Scheduler → KV Cache Manager / BlockPool → Model Runner → GPU

| Stage | Page | Summary |
| --- | --- | --- |
| Basics | [model-states-and-kv-cache](concepts/model-states-and-kv-cache.md) | Weights vs hidden states vs KV cache |
| Scheduler | [scheduler-and-cache-policy](concepts/scheduler-and-cache-policy.md) | Token budget, `max_num_seqs`, preemption vs eviction, offloading |
| KV Cache Manager | [kv-cache-memory](concepts/kv-cache-memory.md) | Per-token KV formula, MHA/GQA/MQA, capacity (main line item 1) |
| BlockPool | [blockpool-block-life-cycle](concepts/blockpool-block-life-cycle.md) | `ref_cnt`, free queue front/end, hash, evict vs preempt |
| Model Runner | [block-table-and-slot-mapping](concepts/block-table-and-slot-mapping.md) | Block Table (read) vs `slot_mapping` (write), the 3-step rule |
| Model Runner | [model-runner-inputs-and-chunked-prefill](concepts/model-runner-inputs-and-chunked-prefill.md) | `input_ids`, `positions`, chunked prefill |
| GPU | [ttft-vs-tpot](concepts/ttft-vs-tpot.md) | What limits TTFT (prefill) vs TPOT (decode) — main line item 2, started 11-23 |
| GPU | [memory-bound-and-batching](concepts/memory-bound-and-batching.md) | Why decode is memory-bound; what batching shares |
| Side (P2) | [speculative-decoding-eagle](concepts/speculative-decoding-eagle.md) | Speculative decoding, EAGLE block drop, lookahead hash |

## Analyses

| Page | Summary |
| --- | --- |
| [issues/vllm-65161](issues/vllm-65161.md) | EAGLE + CPU KV offloading investigation (parked) |
| [experiments/README](experiments/README.md) | Rule: reproducible results only |
| [experiments/CHAT_REPORTED_RESULTS](experiments/CHAT_REPORTED_RESULTS.md) | Earlier reported results, not reproduced here |
| [contributions/README](contributions/README.md) | Upstream PRs (not merged) |
| [reviews/2026-11-05](reviews/2026-11-05.md) | Optional EAGLE closed-book quiz (P2) |
| [reviews/archive-2026-11-04-05](reviews/archive-2026-11-04-05.md) | Old review rows moved out of the queue |

## Raw sources

| Page | Summary |
| --- | --- |
| [raw/](raw/README.md) | Read-only sources. None ingested yet. |

## Assets

[block-table-mapping.svg](assets/block-table-mapping.svg) · [scheduler-three-parts.svg](assets/scheduler-three-parts.svg) · [components-who-decides.svg](assets/components-who-decides.svg) · [kv-budget.svg](assets/kv-budget.svg) · [ttft-vs-tpot.svg](assets/ttft-vs-tpot.svg) · [max-num-batched-tokens.svg](assets/max-num-batched-tokens.svg) · [eagle-lookahead.svg](assets/eagle-lookahead.svg) · [inference-skill-map.svg](assets/inference-skill-map.svg) · [memory-bound-batching.svg](assets/memory-bound-batching.svg)
