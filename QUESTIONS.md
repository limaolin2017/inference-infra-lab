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
| 11-23 | 为什么 50 个用户权重只读一次，KV cache 要读 50 次? | Batching: weights shared, K/V per user | [ttft-vs-tpot](concepts/ttft-vs-tpot.md), [memory-bound-and-batching](concepts/memory-bound-and-batching.md) |

## English questions

| Date | Question | Answer |
| --- | --- | --- |
| 11-23 | Why "How many tokens fit?" and not "does fit"? | "How many tokens" is the subject → no *do* |
| 11-23 | What does "1 KB = 1,024 bytes" mean here? | Convert bytes to KB: ÷ 1,024 |

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

**Pattern:** 9 confidence checks on 11-23: 3 right, 5 partial, 1 miss. Even the right ones need a re-check.

## Unclear areas (from questions)

- **KV memory formula** (5 questions on 11-23): what each factor means, where the numbers come from.
- **Batching / what is shared** (1, plus 2 mistakes in [MISTAKES.md](MISTAKES.md)).
