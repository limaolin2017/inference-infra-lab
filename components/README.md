# Components — who owns what, who decides what

**Status:** component pages added 2026-11-23, after the learner mixed up the Scheduler and `kv_cache_manager`. Built from concept notes plus assistant-shown source, not an independent trace.
**Main path:** Request → **Scheduler → KV Cache Manager → BlockPool → Model Runner** → GPU

Concept pages explain an **idea** (`slot_mapping`, KV budget). Component pages explain a **part of vLLM**: what it owns, what it decides, who calls it. Every page uses the same 4 lines.

```text
Scheduler ──asks──▶ KV Cache Manager ──calls──▶ BlockPool
    │                (front desk)                (warehouse)
    └── SchedulerOutput ──▶ Model Runner ──▶ GPU
```

## Who decides? (the 11-23 confusion)

| Question | Who decides | Who only answers / does |
| --- | --- | --- |
| Which requests run this step? | **Scheduler** | — |
| Are there blocks for this request? | — | **KV Cache Manager** (blocks or `None`) |
| Which physical block is taken? | — | **BlockPool** (front of the free queue) |
| Who gets preempted? | **Scheduler** | KV Cache Manager frees the blocks |
| Where does each K/V get written? | — | **Model Runner** (`slot_mapping`) |

Rule of thumb: **only the Scheduler makes decisions.** The others answer or do the work.

## Pages

| Component | Page |
| --- | --- |
| Scheduler | [scheduler.md](scheduler.md) |
| KV Cache Manager | [kv-cache-manager.md](kv-cache-manager.md) |
| BlockPool | [block-pool.md](block-pool.md) |
| Model Runner | [model-runner.md](model-runner.md) |

Source links are pinned to vLLM commit [6ba1050](https://github.com/vllm-project/vllm/tree/6ba10507e11c012d40051548c07c10c9bc5971d6).
