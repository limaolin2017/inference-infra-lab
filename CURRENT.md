# Current State

**Updated:** 2026-11-04  
**Primary learning track:** job-ready inference engineering; follow [SKILL_MAP.md](SKILL_MAP.md). [vLLM #65161](issues/vllm-65161.md) stays a lower-priority research branch.  
**Next action:** On 2026-11-05, do the brief [closed-book review quiz](reviews/2026-11-05.md) (no hints before the attempt). Then trace one request through Scheduler → KV → Model Runner and build a **public-safe reproducible benchmark** on NVIDIA. OffloadingConnector details are optional if #65161 is already fixed.

## Job-target gap: summary (2026-11-04)

**Read [SKILL_MAP.md](SKILL_MAP.md) before diving into another issue.** We have studied much of the KV/cache/scheduling vocabulary. The biggest remaining job-ready proofs are:

1. **A public-safe NVIDIA performance baseline** (TTFT, TPOT, throughput, workload settings). The user reports **prior successful single-GPU LLM deployment** and **vLLM/SGLang comparison** in real professional work, but no shareable raw data or reproducible benchmark is stored in this repo.
2. **One end-to-end code trace: request → Scheduler → KV manager → Model Runner → GPU**. Specific internals have been discussed, but not independently traced end-to-end.
3. **A profile-based diagnosis and controlled fix**. No reproducible GPU kernel trace or A/B performance case study here.
4. **For serving/platform roles:** Linux/Docker/Kubernetes, metrics, queue and reliability evidence. Prior single-GPU inference deployment counts as real experience, but does not yet demonstrate a monitored multi-node fleet.

**Do not equate issue difficulty with job value.** Cache-eviction policy research (2Q/ARC), EAGLE lookahead edge cases, multi-node networking, or accelerator-specific backends can wait unless they unlock one of these proofs.

**Engineering evidence:** Personal upstream [#61801](https://github.com/vllm-project/vllm/pull/61801) (docs, open), [#64281](https://github.com/vllm-project/vllm/pull/64281) (BlockPool CPU regression, open draft), [#65366](https://github.com/vllm-project/vllm/pull/65366) (tokenizer/API CPU regression, open). See [contributions](contributions/README.md) and [chat-reported experiments](experiments/CHAT_REPORTED_RESULTS.md). None are merged as checked today. Local tests stated in PR descriptions are reported tests, not independent evidence of personal mastery.

**Immediate plan:** Keep tomorrow's brief blind review. Then move to a public-safe GPU benchmark and vertical code tracing. Build on prior vLLM/SGLang deployment, rather than repeating beginner deployment lessons. Do not spend another full session on specialized EAGLE rules unless an actual bug remains.

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
| Lookahead block hashes | 2 · Explained (guided) | Identified T5 after a 4-token block; understood combined Hash(ABCDE), not separate hashes or comparison of draft KV | Answer a new no-hints review question, then read implementation |
| CPU KV offloading connector | 1 · Seen | External tier may report more cached tokens than local GPU; learner has not explained this path independently | Learn an intuitive load scenario, then trace connector lookup and load steps |
| Scheduler: load vs. recompute | 0–1 · Not verified | Cost decision is an open question | Design A/B test with TTFT and load counts |
| LRU / 2Q / ARC | 2 · Explained (earlier sessions) | Basic cache-eviction intuition | Compare behavior using a test trace |
| Preemption / admission control | 2 · Explained (earlier sessions) | Basic scheduling trade-offs | Independently reason through a constrained KV budget |
| CUDA / kernel profiling | 0 · Not evidenced in repo | Real NVIDIA serving is reported, but no public kernel profile or CUDA test | Pick a scoped profiling exercise |

## The three original concept questions

1. **KV cache vs. hidden states:** understood at a basic level; code-level explanation still pending.
2. **Why EAGLE may drop the trailing block:** concept verified, based on the next-token dependence of draft KV.
3. **Why the CPU offload tier may reload that block:** not yet verified; next engineering lesson.

**Extra progress:** Lookahead Hash is now explained with guidance. The learner correctly stated that the combined hash covers the block and known next token, and corrected the initial confusion between token IDs and draft-KV values. A new closed-book example is needed before marking it independently verified.

## Our optimization loop

**Target → Current → Gap → Action → Feedback → Updated Current.** See [LEARNING_PROTOCOL.md](LEARNING_PROTOCOL.md) and [ROADMAP.md](ROADMAP.md). A high number of small PRs is not a substitute for demonstrated core-system skill.

## Current upstream facts

- [Issue #65161](https://github.com/vllm-project/vllm/issues/65161) is an earlier performance report about EAGLE and CPU KV loads.
- [PR #61589](https://github.com/vllm-project/vllm/pull/61589) merged on **2026-10-26**; adds lookahead-aware local prefix hashes.
- [PR #64096](https://github.com/vllm-project/vllm/pull/64096) merged on **2026-11-03**; adds support for compatible KV connectors, including OffloadingConnector.
- Therefore the original issue **may be partly or fully mitigated on current main**. Reproduction is required before planning a fix.
- A separate, verified upstream contribution: [PR #65366](https://github.com/vllm-project/vllm/pull/65366) is **open**, not merged, as checked on 2026-11-04.

## #65161 research checklist (optional; not the job-readiness critical path)

- [x] Explained why EAGLE draft KV at the trailing block boundary can depend on a following token (X vs Y). Verified conceptually in discussion; not yet traced in source.
- [x] Learn basic Lookahead Hash with an example (guided understanding).
- [ ] Pass the closed-book [2026-11-05 review](reviews/2026-11-05.md) without hints.
- [ ] Understand why CPU OffloadingConnector may reload a dropped trailing block.
- [ ] Read `FullAttentionManager.find_longest_cache_hit` and inspect exact drop rules.
- [ ] Trace `Scheduler -> OffloadingConnector.get_num_new_matched_tokens -> load`.
- [ ] Check when `num_hash_lookahead_tokens` is nonzero and when the trailing-block drop is disabled.
- [ ] Build a CPU-only regression test or source-level scenario first.
- [ ] If the issue still reproduces, measure TTFT, CPU-load count, and recomputation cost under controlled settings.
- [ ] Choose **no new PR** if the current implementation already resolves the problem.

## Session handoff template

At the start: read `CURRENT.md`, the linked issue note, and the last journal entry.  
At the end: update **new evidence**, **unresolved gaps**, and **one next action**.

Do not advance a knowledge level without new evidence.
