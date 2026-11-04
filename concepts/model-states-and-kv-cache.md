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

## Why this matters for EAGLE

KV caching preserves the keys and values needed for subsequent attention. It does not normally preserve **all** hidden states that an EAGLE draft head may want. A cached block therefore does not automatically provide the full target-model features required at the drafting boundary.

Separately, EAGLE-style **draft KV** may depend on tokens *after* a cache block. This is a specific prefix-cache safety issue, not merely a missing-hidden-state issue.

## Understanding check

If the same model receives two different prompts, which of weights, hidden states, and KV cache typically change? Why is the KV cache not a complete copy of the hidden states?

## References

- [vLLM: KV cache block manager](https://github.com/vllm-project/vllm/blob/main/vllm/v1/core/single_type_kv_cache_manager.py)
- [vLLM: speculative configuration](https://github.com/vllm-project/vllm/blob/main/vllm/config/speculative.py)
