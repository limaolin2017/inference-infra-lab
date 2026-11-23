# KV Cache Manager

**Status:** component page, 2026-11-23. Role learned 2026-11-23 (guided, after a mix-up with the Scheduler).
**Main path:** Request → Scheduler → **KV Cache Manager** / BlockPool → Model Runner → GPU
**Source:** [`vllm/v1/core/kv_cache_manager.py`](https://github.com/vllm-project/vllm/blob/6ba10507e11c012d40051548c07c10c9bc5971d6/vllm/v1/core/kv_cache_manager.py)

| | |
| --- | --- |
| **Owns** | Which blocks belong to which request (the block lists behind each Block Table) |
| **Decides** | Nothing about scheduling |
| **Answers** | `get_computed_blocks()`: prefix cache hits · `allocate_slots()`: new blocks, or `None` if not enough · `free()`: give a request's blocks back |
| **Called by / calls** | Called by the Scheduler → calls BlockPool to really take / return blocks |

**Analogy:** the front desk (or the restaurant host with the table list). Says "here are your rooms" or "full". Does not choose guests.

## Key rules

- `allocate_slots()` returning `None` is a **fact**, not a decision. The Scheduler then decides: wait, or preempt.
- How many blocks exist at all is fixed at startup by the [KV budget](../concepts/kv-cache-memory.md).

## Common mix-ups

- ❌ "It decides who goes to waiting / running." ✅ It only gives out and takes back blocks.

**Concepts:** [kv-cache-memory](../concepts/kv-cache-memory.md) · [blockpool-block-life-cycle](../concepts/blockpool-block-life-cycle.md)
