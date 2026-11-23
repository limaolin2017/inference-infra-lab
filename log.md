# Log — append-only

One entry per event. Newest at the bottom. Format: `## [YYYY-MM-DD] <type> | <title>`. Recent activity: `grep "^## \[" log.md | tail -5`. Full detail lives in [journal/](journal/).

Entries before 2026-11-23 were backfilled from the journal.

## [2026-11-04] learn | Token budget, EAGLE trailing-block drop, repo audit
See [journal/2026-11-04.md](journal/2026-11-04.md).

## [2026-11-05] review | Morning P0 review (too long, 90 min) → review split rule
See [journal/2026-11-05.md](journal/2026-11-05.md).

## [2026-11-09] review | Last week's review; Block Table / slot mapping concept
See [journal/2026-11-09.md](journal/2026-11-09.md).

## [2026-11-10] learn | Memory-bound decode and batch size
See [journal/2026-11-10.md](journal/2026-11-10.md).

## [2026-11-11] learn | BlockPool block life cycle
See [journal/2026-11-11.md](journal/2026-11-11.md).

## [2026-11-13] code-check | Review + `get_new_blocks()` code check (guided)
See [journal/2026-11-13.md](journal/2026-11-13.md).

## [2026-11-16] review | 3 P0 questions; `slot_mapping` first answer
See [journal/2026-11-16.md](journal/2026-11-16.md).

## [2026-11-17] learn | `slot_mapping` review; KV cache memory formula; new main line
See [journal/2026-11-17.md](journal/2026-11-17.md).

## [2026-11-19] review | Outside the project chat: blocks, no free blocks, `waiting` deque
See [journal/2026-11-19.md](journal/2026-11-19.md).

## [2026-11-23] review | Scheduler 3 parts; Scheduler vs `kv_cache_manager`; preemption trigger
See [journal/2026-11-23.md](journal/2026-11-23.md).

## [2026-11-23] learn | KV cache budget (started, step 1 question open)
See [journal/2026-11-23.md](journal/2026-11-23.md).

## [2026-11-23] schema | Adopt Karpathy LLM Wiki pattern (light)
Added [CLAUDE.md](CLAUDE.md), [index.md](index.md), this log and [raw/](raw/README.md). No files moved.

## [2026-11-23] schema | Add components/ (Scheduler, KV Cache Manager, BlockPool, Model Runner)
One page per vLLM part, same 4 rows. Added to fix the Scheduler vs `kv_cache_manager` mix-up. See [components/](components/README.md).

## [2026-11-23] learn | KV per-token practice; why × 2; KV heads vs KV cache
Added to [kv-cache-memory](concepts/kv-cache-memory.md). Missed the leading 2 → review queue.

## [2026-11-23] learn | Capacity thinking: KV cache vs weight quantization; no-hand-arithmetic rule
See [kv-cache-memory](concepts/kv-cache-memory.md) and [LEARNING_PROTOCOL.md](LEARNING_PROTOCOL.md).

## [2026-11-23] schema | Goal alignment: concepts → CPU test → GPU benchmark
See [CURRENT.md](CURRENT.md#order-of-work-agreed-2026-11-23).

## [2026-11-23] schema | Colab gate with pass criteria (A capacity, B TTFT/TPOT, C prediction)
See [CURRENT.md](CURRENT.md#colab-gate--measurable-agreed-2026-11-23).

## [2026-11-23] schema | Add MISTAKES.md (mistake counts and weak-point patterns)
Rebuilt from the review queue and journal. See [MISTAKES.md](MISTAKES.md).

## [2026-11-23] learn | TTFT vs TPOT started (main line item 2)
See [ttft-vs-tpot](concepts/ttft-vs-tpot.md).

## [2026-11-23] schema | Save teaching SVGs to assets/ and reuse at review
4 pictures added. Rule in [CLAUDE.md](CLAUDE.md).

## [2026-11-23] schema | Add QUESTIONS.md (learner questions by topic)
See [QUESTIONS.md](QUESTIONS.md).

## [2026-11-23] schema | Track confidence checks ("对吗?") in QUESTIONS.md
