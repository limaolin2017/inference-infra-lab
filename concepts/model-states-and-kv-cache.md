# Model Weights, Hidden States, and KV Cache

**Level:** Basic concepts verified; code-level understanding pending.

## Three different kinds of data

| Item | What it is | Changes per request? |
| --- | --- | --- |
| **Weights** | Learned parameters of the network | Normally no during inference |
| **Hidden states** | Intermediate activations computed while processing tokens | Yes |
| **KV cache** | Saved attention keys and values from processed tokens | Yes |

A simple layer picture:

~~~text
input features (hidden states)
     |
     +-- projections with learned W_K, W_V --> K, V --> KV cache
     |
     +-- attention + feed-forward computation --> next hidden states
~~~

A simplified expression is `K = H @ W_K`, `V = H @ W_V`. Real models include multiple layers, heads, and other operations.

## Hidden states → Q/K/V → Attention (2026-11-05 guided recap)

At **each Transformer layer**, the current hidden-state vectors are the inputs to learned projections for **Q, K, and V** (simplified: `Q = H @ W_Q`, `K = H @ W_K`, `V = H @ W_V`). Attention uses **Q against K**, then weights **V**; further layer operations produce new hidden states. This is **not** a single 'first layer only' pipeline.

- **Q is a Query vector, not a user Request.** One Request has many tokens; tokens at different positions produce queries.
- For decoding, the current token's Query uses the **past tokens' keys and values**. vLLM saves K/V **for every relevant layer** as KV Cache, rather than preserving every historical intermediate hidden state just to reconstruct those K/V values.
- If the past K/V are already in cache, the model does **not** need to recompute all 1,000 historical hidden states simply to generate token 1,001. The **new** step still computes new hidden states and projections as it moves through the layers.
- If K/V were lost during recompute preemption, the model may have to reprocess historical token IDs to rebuild the missing K/V. Prefix reuse can reduce that work.

**Learning correction:** hidden states **do participate** in Attention, by producing Q/K/V. K/V cache stores selected derived values, not a copy of all hidden states.

## Why this matters for EAGLE

KV caching preserves the keys and values needed for subsequent attention. It does not normally preserve **all** hidden states that an EAGLE draft head may want. A cached block therefore does not automatically provide the full target-model features required at the drafting boundary.

Separately, EAGLE-style **draft KV** may depend on tokens *after* a cache block. This is a specific prefix-cache safety issue, not merely a missing-hidden-state issue.

## Understanding check

If the same model receives two different prompts, which of weights, hidden states, and KV cache typically change? Why is the KV cache not a complete copy of the hidden states?

## References

- [vLLM: KV cache block manager](https://github.com/vllm-project/vllm/blob/main/vllm/v1/core/single_type_kv_cache_manager.py)
- [vLLM: speculative configuration](https://github.com/vllm-project/vllm/blob/main/vllm/config/speculative.py)
