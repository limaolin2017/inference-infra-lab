# Questions — what the learner asked

**Purpose:** a question shows what was unclear. Log every learner question with its topic, so unclear areas show up next to [MISTAKES.md](MISTAKES.md). Double backup: this file + project memory.
**Started:** 2026-11-23.

## Infra questions

| Date | Question (learner's words, short) | Topic | Answer in |
| --- | --- | --- | --- |
| 11-23 | `prepend` 的中文是啥? | Scheduler: `prepend_request` | [scheduler](components/scheduler.md) |
| 11-23 | components 不是组成部分吗? 中文叫啥? | Repo / wording | [components/](components/README.md) |
| 11-23 | 这是在考哪个知识点? (KV bytes per token) | KV memory formula | [kv-cache-memory](concepts/kv-cache-memory.md) |
| 11-23 | 为啥这里要乘以 2? | KV formula: K and V | [kv-cache-memory § Why × 2](concepts/kv-cache-memory.md) |
| 11-23 | KV heads 和 KV cache 的区别? | KV heads vs KV cache | [kv-cache-memory](concepts/kv-cache-memory.md) |
| 11-23 | 我不知道 Llama-3-8B 的架构，怎么算? | Finding `config.json` fields | [kv-cache-memory](concepts/kv-cache-memory.md) |
| 11-23 | 为啥要换算成 KB? | Units (bytes vs GB) | journal 11-23 |
| 11-23 | 如果有 4 个用户，为啥不是乘以 4? | Capacity: need vs fix | [kv-cache-memory § Fit more users](concepts/kv-cache-memory.md) |
| 11-23 | 模型权重缩小就是量化吗? | KV cache vs weight quantization | [kv-cache-memory § Fit more users](concepts/kv-cache-memory.md) |
| 11-23 | 什么叫做 check B? (asked **3×**, in Chinese) → the letter label does not stick; use full names (容量估算 / 延迟检查 / 运行前预测) | Colab gate | [CURRENT.md § Colab gate](CURRENT.md) |
| 11-23 | TTFT 除了 memory-bound，还有一个是什么 bound? (forgot) | TTFT vs TPOT: compute vs memory | [ttft-vs-tpot](concepts/ttft-vs-tpot.md) |
| 11-23 | 什么是延迟检查? (after the rename — the gate idea itself is unclear) | Colab gate / latency | [CURRENT.md § Colab gate](CURRENT.md) |
| 11-23 | 明天会学什么新概念? | Plan → `max_num_seqs` | [CURRENT.md](CURRENT.md) |
| 11-23 | 为啥学过了又要学一遍? (chunked prefill) | Learning method | journal 11-23 |
| 11-23 | 我的理解对不对? 这个图没看懂，用中文解释 (max_num_batched_tokens picture) | `max_num_batched_tokens` | [ttft-vs-tpot](concepts/ttft-vs-tpot.md) |
| 11-23 | 为什么 50 个用户权重只读一次，KV cache 要读 50 次? | Batching: weights shared, K/V per user | [ttft-vs-tpot](concepts/ttft-vs-tpot.md), [memory-bound-and-batching](concepts/memory-bound-and-batching.md) |
| 11-24 | 为什么是计算 prefill 而不是加载? prefill 不是只做一次吗? | Prefill computes vs decode reads | [ttft-vs-tpot](concepts/ttft-vs-tpot.md), journal 11-24 |
| 11-24 | 原来的问题是啥来着? (lost track after 2 side questions) | Learning flow | journal 11-24 |
| 11-24 | 为啥 decode 是 memory 瓶颈呢? | Decode memory-bound (all weights moved for 1 token) | [memory-bound-and-batching](concepts/memory-bound-and-batching.md) |
| 11-24 | FCFS 是啥意思? | Scheduler policy: First-Come, First-Served | [scheduler](components/scheduler.md) |
| 11-24 | KV formula: 最后乘 16 还是多少? | Bits vs bytes | [kv-cache-memory](concepts/kv-cache-memory.md) |

## English questions

| Date | Question | Answer |
| --- | --- | --- |
| 11-23 | Why "How many tokens fit?" and not "does fit"? | "How many tokens" is the subject → no *do* |
| 11-23 | What does "1 KB = 1,024 bytes" mean here? | Convert bytes to KB: ÷ 1,024 |
| 11-23 | "knob" 中文是啥? | 旋钮 → in infra: a setting you can tune (可调参数) |
| 11-24 | FCFS stands for? | First-Come, First-Served (先来先服务) |

## Confidence checks ("对吗? / 我的理解对吗?")

When the learner ends with "对吗?", confidence is low, even if the answer is right. **Right + 对吗 = right but unsure → re-check on a later day.** Added 2026-11-23 (learner's idea).

| Date | Learner's claim (short) | Result | Re-check |
| --- | --- | --- | --- |
| 11-23 | `kv_cache_manager` gives out / takes back blocks; Scheduler asks it — 对吧? | Right | 11-24 |
| 11-23 | No blocks for new C → C waits; or Scheduler may preempt running B to let C in — 对吗? | Partial (new request never preempts in FCFS) | 11-24 |
| 11-23 | 32 × 8 × 128 × 2 — 对吗? | Partial (missed the K/V 2) | 11-24 |
| 11-23 | 2 × 32 × 8 × 128 × 16 — 对吗? | Partial (bits, not bytes) | 11-24 |
| 11-23 | You are teaching me a rough estimate, like memory for 4K tokens — 对吗? | Right | — (meta) |
| 11-23 | 4 users → × 4 — 对吧? | Miss (need, not fix) | 11-24 |
| 11-23 | Shrinking weights = quantization — 对吗? | Right (+ KV cache quantization too) | 11-24 |
| 11-23 | 10,000-token prompt → TTFT slow, must prefill 10,000 tokens — 对吧? | Right | 11-24 |
| 11-23 | K/V read 50× because it depends on cache hits — 我的理解对吗? | Partial (K/V is per-user data) | 11-24 |
| 11-23 | Each user's K/V is only theirs and different, so it's read 50 times — 对吧? | Right (after explanation) | 11-24 |
| 11-23 | Same step = prefill chunk and decode are one block — 对吗? | Partial: one batch / one GPU pass, not one KV block | 11-24 |
| 11-23 | More steps → whole prefill later → TTFT slower; prefill a piece, decode a piece… — 对吧? | Reason right; "alternating" still wrong (same step) | 11-24 |
| 11-23 | Long-prompt TTFT gets slower with chunking — 吧? | Partial (right conclusion; reason missing: more steps, shared with decode) | 11-24 |
| 11-23 | Chunked prefill = split prefill into fixed-size pieces, like 8 or 2 — 我记得好像? | Partial (chunk size comes from the per-step token budget; missed the key point: chunks share a step with decode tokens) | 11-24 (new lesson) |
| 11-23 eve | In decode, does every token reload the prefill's K/V? | Mostly right (+ tokens made so far) | 11-24 ✅ (re-asked as "TPOT loads prefill's K/V") |
| 11-23 eve | 因为 prefill 计算很大，每次 token 加载的内容越来越长 — 对吗? | Miss (side-by-side, not cause → effect) | 11-24 ❌ again (Q1 pt 2) → restated right |
| 11-24 | TPOT is decode, so it loads the K/V prefill already computed — 对吗? | Right (+ tokens made so far) | later |
| 11-24 | Chat users' old K/V read is unrelated; the knob only affects new users' prefill — 对吗? | Right (guided) | 11-25 |
| 11-24 | Moving all weights from VRAM depends on memory bandwidth — 是吗? | Right | — |
| 11-24 | Batch 1 → 50 decode → closer to compute-bound — 对不对? | Right (wavered; still mostly memory-bound) | later |
| 11-24 | D's blocks go to the free queue, not held — 对吧? | Right (guided) | 11-25 |

**Pattern:** 14 confidence checks on 11-23: 4 right, 9 partial, 1 miss. Even the right ones need a re-check. 11-23 evening: 2 (1 mostly right, 1 miss). 11-24: 5, all right (2 guided).

**11-24 re-check of the 11-23 rows:** `kv_cache_manager` role ✅ · missing K/V 2 ✅ · bits vs bytes ❌ · shrinking weights = quantization ✅ · K/V read per user ✅ · long prompt → slow TTFT ✅ (implicit) · chunked prefill one step ✅ (first token ±1 step) · new C preempts B — not re-asked · 4 users → × 4 — not re-asked.

## Unclear areas (from questions)

- **KV memory formula** (5 questions on 11-23): what each factor means, where the numbers come from.
- **Batching / what is shared** (1, plus 2 mistakes in [MISTAKES.md](MISTAKES.md)).
- **Compute vs read, and what limits TTFT / TPOT** (2 questions on 11-24, plus 3 mistakes): prefill computes, decode reads — 先算后读.
