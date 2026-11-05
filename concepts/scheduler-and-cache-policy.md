# Scheduling, Cache Eviction, and KV Offloading

**Level:** Mixed. Prior study completed, but independent code-level verification remains.

## Token Budget — basic concept checked (2026-11-04)

**Mental model:** each request is a vehicle. The Scheduler has a per-step budget for how many token positions to compute **across all requests**, not how many people fit in one vehicle and not how many total output tokens a model can generate.

- **Token Budget** = upper bound on token computation scheduled in this step. A request can receive only part of its remaining work when chunking is allowed.
- **`max_num_seqs`** = cap on concurrently running requests (a different limit).
- **KV capacity** = memory available for new KV state (a third, independent limit).
- **Preemption** is one possible response to KV pressure; an unused, evictable cache block may be reclaimed without preempting a running request.
- **Unused budget is possible**: another constraint can stop scheduling before token budget is fully consumed.

Examples answered correctly in guided discussion:

| Scenario | Learner response | Evaluation |
| --- | --- | --- |
| Budget 10, A uses 6 | 4 remain | Correct |
| Budget 10, A uses 6, B needs 8, chunked prefill | B can use 4 this step | Correct under stated assumptions |
| `max_num_seqs=2`; A and B running | C cannot join while both slots remain occupied | Correct |
| KV full despite spare token budget/slots | New computation needs KV capacity; preemption may be relevant | Correct basic intuition |
| Ten unused/evictable old cache blocks | Reclaim blocks without preemption | Correct |

**Scope:** These were short, guided concept checks. An earlier three-request allocation answer was incorrect and corrected in discussion. This is **not** an independent delayed H pass, source trace, Agent-designed test, or CPU benchmark. Next: read a real `schedule()` branch with a fresh case.

**Source:** [Scheduler.schedule(), pinned vLLM commit 6ba1050](https://github.com/vllm-project/vllm/blob/6ba10507e11c012d40051548c07c10c9bc5971d6/vllm/v1/core/sched/scheduler.py#L737-L760). In this revision, `max_num_scheduled_tokens` and `max_num_batched_tokens` can be separate scheduling constraints. The simple examples ignore async and speculative details.

## Eviction vs. request preemption — clarified 2026-11-05

- **Eviction:** reclaim a **reusable, unreferenced cached KV block** (for example `ref_cnt=0`). The block may still hold valid prefix data even though no active request uses it. The cache contents may be overwritten for a new allocation. No running request needs to stop.
- **Preemption:** pause a **running Request**, not an individual KV block. Its active KV allocations are released. The request keeps its token sequence and can be scheduled again later; missing KV must be recomputed unless some cached prefix can be reused.
- **Waiting is not preemption.** A new waiting Request short of KV space can remain queued. A running Request that cannot allocate more KV can trigger the scheduler's preemption path, often selecting another running request. Which request is selected depends on the scheduler policy.
- **Key pitfall:** a full KV block is not a finished unit of work. Its values can still be needed by later tokens. Also, preempting the **only** running request does not magically make a too-long request fit in memory.
- **Example (illustrative, not measured):** A holds 3 blocks, C holds 2, GPU capacity 5. A needs one more. Preempting C can release room for A, but C may later need recomputation. If A completes, its blocks become unreferenced and may be reused without preempting A.

These are **guided concept examples**, not an independent trace of a particular vLLM revision. For source proof, follow [CURRENT.md](../CURRENT.md).

## Core ideas

- **Admission control:** decide whether a waiting request can start using limited execution slots and memory.
- **Preemption:** pause or displace a request to make progress on another request. Resuming may require recomputation or restoring state.
- **Prefix cache:** reuse cached KV for a matching sequence of previous tokens.
- **LRU:** evict data that was used least recently.
- **2Q:** use different queues to avoid treating every one-time access as valuable.
- **ARC:** adapt the balance of recency and frequency using information about past evictions.
- **Offloading:** place KV on another memory tier, such as CPU RAM, to save GPU memory.

## The important trade-off

A cache hit is not automatically a latency win.

For a small tail:

~~~text
Option A: GPU recomputes a few tokens
Option B: CPU cache lookup + transfer + scheduling waits
~~~

Which is faster depends on transfer latency, amount of KV, GPU workload, and scheduling overhead.

## Engineering questions

1. Where does vLLM count a local prefix hit?
2. Where does the connector report additional cached tokens?
3. When does a request enter `WAITING_FOR_REMOTE_KVS`?
4. How would you measure load-vs-recompute without confounding other requests?

## References

- [Scheduler implementation](https://github.com/vllm-project/vllm/blob/main/vllm/v1/core/sched/scheduler.py)
- [Offloading connector](https://github.com/vllm-project/vllm/blob/main/vllm/distributed/kv_transfer/kv_connector/v1/offloading_connector.py)
- [KV cache manager](https://github.com/vllm-project/vllm/blob/main/vllm/v1/core/kv_cache_manager.py)
