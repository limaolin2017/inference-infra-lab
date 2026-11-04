# Review Queue — Unclear Points

This file tracks **concepts that caused confusion**, not every question asked. Learning progress is evidence-based.

**Review rule:** At the next study session, ask the linked quiz questions **before showing any notes, hints, or answers**. After an answer, give a Pass / Partial / Miss grade, explain the specific error, and schedule another example for missed concepts.

| Topic / source of confusion | Review question focus | Last session | State | Next review |
| --- | --- | --- | --- | --- |
| EAGLE trailing-block drop | Why identical block tokens may not prove identical draft KV | 2026-11-04 | Understood conceptually; retention untested | 2026-11-05 |
| Lookahead Hash: what is "ahead"? | Identify the first token after a full block | 2026-11-04 | Corrected and explained | 2026-11-05 |
| Lookahead Hash: hash vs. KV | Is the next token's **ID** included, or is its Draft KV compared? | 2026-11-04 | Corrected with guidance; high-priority retest | 2026-11-05 |
| Target KV vs. EAGLE Draft KV | Which is changed by a different token after a shared prefix? | 2026-11-04 | Explained; retention untested | 2026-11-05 |
| CPU OffloadingConnector reload | Why could a cache layer load data another layer excluded from local hits? | 2026-11-04 | **Not yet learned; P2 side quest** | Only if current-main issue persists |

## Job-readiness review queue (after the 2026-11-05 blind quiz)

These are *future* proof tasks, not scored questions yet.

1. Explain one prefill and one decode path through `Scheduler → KVCacheManager → ModelRunner` without prompts.
2. Given a model and batch shape, derive approximate K/V memory and identify the memory limit.
3. Set up repeatable single NVIDIA GPU serving with TTFT, TPOT and throughput.
4. Explain an actual GPU profile before choosing an optimization.
5. If targeting serving/platform work, demonstrate Linux/Docker/K8s and service metrics.

Keep new material out of tomorrow's existing closed-book quiz. See [SKILL_MAP.md](SKILL_MAP.md).

## Review outcome rules

- **Pass:** independently explains the cause and applies it to a fresh example.
- **Partial:** correct conclusion but confuses mechanism (e.g. token ID vs. Draft KV).
- **Miss:** wrong conclusion or cannot explain.
- **Unattempted:** no review yet; do not claim a pass.

Do **not** pre-fill the answers in the [2026-11-05 quiz](reviews/2026-11-05.md). Record explanations **after** the learner responds.

Limit the review to about five focused questions. If a topic passes twice on different days, move it to a less frequent review.
