# KV cache memory — formula and capacity

**Component pages:** [kv-cache-manager](../components/kv-cache-manager.md)

**Status:** concept note, 2026-11-17 (guided). Main line item 1. Not yet checked against a real vLLM startup log.

**Main path:** Request → Scheduler → **KV Cache Manager / BlockPool** → Model Runner → GPU

## One-sentence idea

Every token keeps one K and one V vector per KV head per layer, so KV memory grows linearly with tokens; the number of **KV heads** (MHA / GQA / MQA) is the biggest model-side knob.

## Formula

~~~text
per token   = 2 × layers × KV heads × head_dim × bytes
per request = per token × tokens
~~~

`2` = K and V.

## Example (32 layers, head_dim 128, FP16 = 2 bytes)

| Type | KV heads | Per token | 8K tokens |
| --- | --- | --- | --- |
| MHA | 32 | 512 KiB | 4 GiB |
| GQA | 8 | 128 KiB | 1 GiB |
| MQA | 1 | 16 KiB | 0.125 GiB |

Check: 2 × 32 × 32 × 128 × 2 B = 524,288 B = 512 KiB; × 8,192 tokens = 4 GiB.

## Common confusions

- MHA / GQA / MQA = different **KV head counts**. Blocking is a separate thing (PagedAttention memory management).
- KV heads are **projected** with learned weights (`W_K`, `W_V`), not "split" from one K.
- Weights hold only `W_K`, `W_V`. K and V are per-request results — see [model states](model-states-and-kv-cache.md).

## Where the KV budget comes from (vLLM)

At startup vLLM runs a profiling pass, measures memory for weights + activations, and reserves **the rest** as empty KV blocks.

Why up front, not per request:

1. GPU allocation is slow; a decode step is only a few ms.
2. Avoid fragmentation.
3. The Scheduler needs a fixed total capacity to decide whether to admit a request.

It is **not** for warm-up.

## Next checks

- Paper exercise: M1 Pro 16 GB, a small model — how many 4K-token requests fit at once?
- Later (main line item 4): compare the formula with the KV cache size in a vLLM startup log.

## Reference

- KV Cache Handbook, pp. 7–9.
