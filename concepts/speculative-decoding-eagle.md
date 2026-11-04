# Speculative Decoding and EAGLE

**Level:** Explained. Independent understanding check pending.

## Basic idea

Normal generation runs a large model one token at a time.

Speculative decoding uses a fast **drafter** to propose multiple candidate tokens. The **target model** then checks several positions in one forward pass. Accepted tokens advance generation, reducing the number of sequential target steps.

The draft does **not** replace target-model verification. With the correct acceptance algorithm, stochastic speculative decoding preserves the target distribution.

Speedup is **not guaranteed**. A slow drafter, low acceptance rate, or expensive verification can remove the benefit.

## What makes EAGLE different?

EAGLE uses target-model hidden-state features in a lightweight drafting network. It is more specific than the generic picture of a separate small language model.

A deployment can load a target model and an EAGLE draft component inside one vLLM service, potentially on one GPU if memory allows.

## Why the last prefix block matters

For EAGLE-style drafters, the draft KV at a block's last position can depend on the token after that block.

Consider block size 4:

~~~text
Request 1: [A B C D] E
Request 2: [A B C D] F
~~~

A legacy hash of `ABCD` matches both requests. But the cached **draft** KV may differ because the next token is E in one case and F in the other.

The conservative legacy approach drops the trailing cache-hit unit and recomputes it. This does **not** necessarily erase the physical block.

**Lookahead hashes** include the relevant following token(s) in the cache key. This can make safe reuse possible without an automatic trailing-block drop.

## Understanding checks

1. What is the difference between a draft proposal and a target verification?
2. Why does a legacy hash of `ABCD` not prove that an EAGLE draft KV made with `E` is safe for `ABCD + F`?
3. Why is better cache reuse not automatically faster under every workload?

## References

- [Lookahead local hashes: PR #61589](https://github.com/vllm-project/vllm/pull/61589)
- [Lookahead KV connector support: PR #64096](https://github.com/vllm-project/vllm/pull/64096)
- [vLLM speculative configuration](https://github.com/vllm-project/vllm/blob/main/vllm/config/speculative.py)
