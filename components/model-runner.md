# Model Runner

**Status:** component page, 2026-11-23. Inputs learned 2026-11-13 / 11-16 (guided). Source not yet located by the learner.
**Main path:** Request → Scheduler → KV Cache Manager / BlockPool → **Model Runner** → GPU
**Source:** [`vllm/v1/worker/gpu_model_runner.py`](https://github.com/vllm-project/vllm/blob/6ba10507e11c012d40051548c07c10c9bc5971d6/vllm/v1/worker/gpu_model_runner.py)

| | |
| --- | --- |
| **Owns** | GPU-side input buffers: `input_ids`, `positions`, Block Table, `slot_mapping` |
| **Decides** | Nothing. Runs exactly what `SchedulerOutput` says |
| **Does** | Builds the inputs → runs the model forward on the GPU → K/V written to slots, sampled tokens returned |
| **Called by** | The worker, with `SchedulerOutput` from the Scheduler |

**Analogy:** the kitchen. Cooks the orders it is given.

## Key rules

- Block Table = per request, for **reading** past K/V. `slot_mapping` = per new token, for **writing** K/V.
- Slot = physical block × `block_size` + (pos % `block_size`). Count pos from 0.
- Decode: `slot_mapping` has **1** number per request.

**Concepts:** [block-table-and-slot-mapping](../concepts/block-table-and-slot-mapping.md) · [model-runner-inputs-and-chunked-prefill](../concepts/model-runner-inputs-and-chunked-prefill.md)
