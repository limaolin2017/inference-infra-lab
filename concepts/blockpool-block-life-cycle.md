# BlockPool — block life cycle (`ref_cnt`, free queue, hash)

**Trunk position:** Scheduler → **KV Cache Manager → BlockPool** → Model Runner. The Scheduler talks to `KVCacheManager` (front desk); `KVCacheManager` calls `BlockPool` (warehouse), which really moves blocks. Builds on [Scheduler and cache policy](scheduler-and-cache-policy.md) and [Block Table / slot mapping](block-table-and-slot-mapping.md). Concept learned 2026-11-11 (guided). **Code check still pending.**

## Why it matters at work

A low prefix-cache hit rate is a common production problem. The free-queue order decides **which old K/V survives longest**. Preemption, prefix hits and "out of KV memory" all happen at this layer.

## Four concepts (hotel analogy)

| Concept | Meaning | Hotel |
| --- | --- | --- |
| block | fixed-size GPU memory chunk holding K/V for `block_size` tokens | a room |
| `ref_cnt` | how many requests use this block now | number of guests |
| free queue | all `ref_cnt = 0` blocks; new blocks are taken from the **front** | list of empty rooms |
| hash | fingerprint of the prefix whose K/V is inside; lets same-prefix requests reuse it | a label on the door |

**Key fact:** a block in the free queue still holds its K/V. It is only *allowed* to be taken. Until it is taken, a same-prefix request can still hit it.

## Three actions (pinned commit `c2320e3`)

| Action | Function | `ref_cnt` | Free queue |
| --- | --- | --- | --- |
| Allocate a new block | [`get_new_blocks()`](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/core/block_pool.py#L681-L722) | 0 → 1 | `popleft_n` from the **front**; an old hash is evicted first (`_maybe_evict_cached_block`) |
| Prefix hit (reuse) | [`touch()`](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/core/block_pool.py#L774-L792) | +1 | if it was 0, `remove(block)` — from **any position** |
| Release | [`free_blocks()`](https://github.com/vllm-project/vllm/blob/c2320e37f14420a1157967362b56c899a726d84d/vllm/v1/core/block_pool.py#L798-L839) | −1 | at 0: no hash → `prepend_n` (**front**, reuse first); has hash → `append_n` (**end**, LRU, lives longest) |

## Front vs end — the point

- `get_new_blocks()` takes **only from the front**. `touch()` can take from **anywhere**.
- So the end is not about "E can get it out". It is about **not letting another request take it first** and overwrite the prefix K/V.
- Learner's own restatement (2026-11-11): *"If Y is at the front, others take it first and overwrite the 'dog' K/V, even though E could have used it. At the end, others take other blocks first; Y is taken last, so it is more likely still there for E."*

## Next proof (code check, learner-located)

Open the 3 functions. In each, find the line that changes `ref_cnt` and the free-queue action. Bonus: in `get_new_blocks()`, what does `_maybe_evict_cached_block()` do to an old hash before the block is handed out?
