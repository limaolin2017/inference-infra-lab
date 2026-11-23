# BlockPool

**Status:** component page, 2026-11-23. Life cycle learned 2026-11-11; `get_new_blocks()` code check 2026-11-13 (guided).
**Main path:** Request → Scheduler → KV Cache Manager / **BlockPool** → Model Runner → GPU
**Source:** [`vllm/v1/core/block_pool.py`](https://github.com/vllm-project/vllm/blob/6ba10507e11c012d40051548c07c10c9bc5971d6/vllm/v1/core/block_pool.py)

| | |
| --- | --- |
| **Owns** | Every physical KV block, `free_block_queue`, the hash → block lookup (prefix cache) |
| **Decides** | Nothing about requests. Only *which* free block: always the **front** of the free queue |
| **Does** | `get_new_blocks()` → `popleft_n()` (and evicts the old hash) · `free_blocks()` → `append_n()` (hashed block to the **end**) · `touch()` → take a cached block out of the free queue, `ref_cnt` + 1 |
| **Called by** | KV Cache Manager only. The Scheduler never talks to it directly. |

**Analogy:** the warehouse behind the front desk.

## Key rules

- A block with `ref_cnt` 0 goes back to the free queue, but its K/V stays until a new owner overwrites it.
- Hashed blocks go to the **end** so they survive longer for prefix reuse.
- **Evict** = remove a free block's hash label (BlockPool). **Preempt** = stop a running request (Scheduler). Different parts.

**Concepts:** [blockpool-block-life-cycle](../concepts/blockpool-block-life-cycle.md)
