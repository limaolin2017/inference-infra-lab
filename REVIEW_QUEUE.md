# Review Queue — Unclear Points

This file tracks **concepts that caused confusion**, not every question asked. Learning progress is evidence-based.

**Review rule:** Prioritize the **Scheduler → KV Manager → Model Runner** main path. Ask a short unseen question when it helps learning, before showing notes or hints. Record Pass / Partial / Miss after the attempt. **The older EAGLE quiz is optional (P2), not a blocker.**

| Topic / source of confusion | Review question focus | Last session | State | Next review |
| --- | --- | --- | --- | --- |
| EAGLE trailing-block drop | Why identical block tokens may not prove identical draft KV | 2026-11-04 | Understood conceptually; retention untested | 2026-11-05 |
| Lookahead Hash: what is "ahead"? | Identify the first token after a full block | 2026-11-04 | Corrected and explained | 2026-11-05 |
| Lookahead Hash: hash vs. KV | Is the next token's **ID** included, or is its Draft KV compared? | 2026-11-04 | Corrected with guidance; high-priority retest | 2026-11-05 |
| Target KV vs. EAGLE Draft KV | Which is changed by a different token after a shared prefix? | 2026-11-04 | Explained; retention untested | 2026-11-05 |
| CPU OffloadingConnector reload | Why could a cache layer load data another layer excluded from local hits? | 2026-11-04 | **Not yet learned; P2 side quest** | Only if current-main issue persists |

## Main-path review queue (P0, next)

These are *future* proof tasks, not scored questions yet. Start with items 1 and 2, rather than studying more EAGLE edge cases.

1. Explain one prefill and one decode path through `Scheduler → KVCacheManager → ModelRunner` without prompts.
2. Given a model and batch shape, derive approximate K/V memory and identify the memory limit.
3. **Deferred until suitable GPU access:** repeatable single NVIDIA GPU serving with TTFT, TPOT and throughput.
4. **Deferred until suitable GPU access:** explain an actual GPU profile before choosing an optimization.
5. If targeting serving/platform work, demonstrate Linux/Docker/K8s and service metrics.

Keep the earlier [2026-11-05 EAGLE quiz](reviews/2026-11-05.md) unchanged as an **optional P2 review**. See [SKILL_MAP.md](SKILL_MAP.md) for the trunk.

## Review outcome rules

- **Pass:** independently explains the cause and applies it to a fresh example.
- **Partial:** correct conclusion but confuses mechanism (e.g. token ID vs. Draft KV).
- **Miss:** wrong conclusion or cannot explain.
- **Unattempted:** no review yet; do not claim a pass.

Do **not** pre-fill answers in any blind quiz, including the [optional EAGLE quiz](reviews/2026-11-05.md). Record explanations **after** a learner response.

Limit the review to about five focused questions. If a topic passes twice on different days, move it to a less frequent review.
