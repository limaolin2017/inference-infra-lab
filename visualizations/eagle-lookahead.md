# EAGLE Lookahead Hash — SVG Animation

The **SMIL SVG animation** below plays directly in supported GitHub Markdown views.

![EAGLE legacy versus lookahead hash animation](../assets/eagle-lookahead.svg)

## What the animation means

- Legacy: a key such as `H(context, ABCD)` does not distinguish a following `E` from `F`. The **EAGLE draft-side** boundary state can differ, so a trailing match may be excluded. CPU offloading may then need to reload data.
- Lookahead: a key such as `H(context, ABCD, E)` includes the next token ID. A matching context and next token can allow safe GPU cache reuse when the connector supports it.
- This is **a simplified concept**, not a source trace or benchmark. It does not claim that all CPU loads disappear. The target model's prefix KV and EAGLE draft-side boundary state are different things.

## Research status

PRs [#61589](https://github.com/vllm-project/vllm/pull/61589) and [#64096](https://github.com/vllm-project/vllm/pull/64096) are merged. The original [Issue #65161](https://github.com/vllm-project/vllm/issues/65161) still needs current-main verification. **No benchmark or new fix has been run here.**

**Next:** trace `FullAttentionManager → Scheduler → OffloadingConnector`, build a regression test, and compare actual transfers and TTFT before writing a PR.
