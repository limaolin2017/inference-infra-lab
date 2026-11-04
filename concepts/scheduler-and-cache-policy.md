# Scheduling, Cache Eviction, and KV Offloading

**Level:** Mixed. Prior study completed, but independent code-level verification remains.

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
