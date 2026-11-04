# Current State

**Updated:** 2026-11-04  
**Primary topic:** [vLLM #65161](issues/vllm-65161.md) — EAGLE, prefix caching, and CPU KV offloading  
**Next action:** Learn lookahead block hashes, then trace the Scheduler and OffloadingConnector path before attempting reproduction.

## Knowledge checkpoints

These are conservative self-assessments from the current learning discussion. They are **not** proof of production-level ability.

| Topic | Level | What is known | Next evidence |
| --- | --- | --- | --- |
| Weights vs. hidden states | 3 · Verified (basic) | Weights are learned parameters; hidden states are run-time activations | Explain how layer projections produce K and V |
| KV cache | 2 · Explained | Historical attention K/V can be reused | Trace a block from allocation through prefix hit |
| Prefix caching / block hashes | 2 · Explained | Matching prefix blocks can save recomputation | Explain chained hashes and boundary limits |
| Speculative decoding | 2 · Explained | A drafter proposes tokens and a target verifies them | Explain acceptance and speed trade-offs |
| EAGLE draft model | 2 · Explained | Drafting uses target-model features | Explain why draft KV depends on lookahead |
| EAGLE trailing-block drop | 3 · Verified (concept) | Explained that the last token's draft KV may depend on the next token (X vs Y), even when the block's own tokens match | Read code and verify exact cache-hit boundary and conditions |
| Lookahead block hashes | 1 · Seen | Hashes can include later tokens to prove reuse | Read implementation and build a small example |
| CPU KV offloading connector | 1 · Seen | External tier can report more cached tokens than local GPU tier | Trace connector lookup and load steps |
| Scheduler: load vs. recompute | 0–1 · Not verified | Cost decision is an open question | Design A/B test with TTFT and load counts |
| LRU / 2Q / ARC | 2 · Explained (earlier sessions) | Basic cache-eviction intuition | Compare behavior using a test trace |
| Preemption / admission control | 2 · Explained (earlier sessions) | Basic scheduling trade-offs | Independently reason through a constrained KV budget |
| CUDA / kernel profiling | 0 · Not started here | No demonstrated experiment in this repo | Pick a scoped profiling exercise |

## Current upstream facts

- [Issue #65161](https://github.com/vllm-project/vllm/issues/65161) is an earlier performance report about EAGLE and CPU KV loads.
- [PR #61589](https://github.com/vllm-project/vllm/pull/61589) merged on **2026-10-26**; adds lookahead-aware local prefix hashes.
- [PR #64096](https://github.com/vllm-project/vllm/pull/64096) merged on **2026-11-03**; adds support for compatible KV connectors, including OffloadingConnector.
- Therefore the original issue **may be partly or fully mitigated on current main**. Reproduction is required before planning a fix.
- A separate, verified upstream contribution: [PR #65366](https://github.com/vllm-project/vllm/pull/65366) is **open**, not merged, as checked on 2026-11-04.

## Next session — exact steps

- [x] Explained why EAGLE draft KV at the trailing block boundary can depend on a following token (X vs Y). Verified conceptually in discussion; not yet traced in source.
- [ ] Learn lookahead block hashes with a new example, then read `FullAttentionManager.find_longest_cache_hit`.
- [ ] Trace `Scheduler -> OffloadingConnector.get_num_new_matched_tokens -> load`.
- [ ] Check when `num_hash_lookahead_tokens` is nonzero and when the trailing-block drop is disabled.
- [ ] Build a CPU-only regression test or source-level scenario first.
- [ ] If the issue still reproduces, measure TTFT, CPU-load count, and recomputation cost under controlled settings.
- [ ] Choose **no new PR** if the current implementation already resolves the problem.

## Session handoff template

At the start: read `CURRENT.md`, the linked issue note, and the last journal entry.  
At the end: update **new evidence**, **unresolved gaps**, and **one next action**.

Do not advance a knowledge level without new evidence.
