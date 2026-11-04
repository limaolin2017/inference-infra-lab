# Chat-Reported Engineering Evidence — Not Yet Reproduced Here

**Audit:** 2026-11-04. This is a record of earlier ChatGPT learning discussions, not a benchmark dataset. No result here is independently reproducible from files currently in this repository. The words "reported" and "simulation" are intentional.

| Date / thread | Topic | Reported result | Evidence status | What is needed |
| --- | --- | --- | --- | --- |
| Before Oct | Professional single-GPU LLM serving and vLLM vs SGLang benchmark | Personally deployed a model, compared TTFT, TPOT and throughput, and selected a serving framework for a real project | **User-reported professional experience**, not independently checked from public artifacts; no client details here | Recreate a safe benchmark using public model, test harness and data |
| Before Nov | PyTorch-to-ONNX inference conversion | Personally converted and compared inference behavior/performance in a professional project | **User-reported real engineering**, numeric gains unavailable here | Publish independent small-model ONNX/vs-PyTorch comparison |
| Oct 28 | BlockPool / free queue negative counts | Base failed new negative-count regressions; fix passed; upstream draft [#64281](https://github.com/vllm-project/vllm/pull/64281) reports 337+49 CPU tests | PR exists and has details; GPU and E2E not tested | Re-run current commit tests and add artifacts only if needed |
| Oct–Nov | 2Q / ARC cache-hit promotion | Local tests reported two fails before fix and four passes overall in one setup; change discussed against [upstream #40270](https://github.com/vllm-project/vllm/pull/40270) | Conversation report; NOT a verified merged personal PR | Preserve diff/test logs and confirm owner/upstream status |
| Oct 30 – Nov 3 | KV-pool saturation / admission control ([issue #64460](https://github.com/vllm-project/vllm/issues/64460)) | CPU synthetic scenarios with ~32K KV capacity, varying `max_num_seqs`, 120 requests and output length; higher admission sometimes caused more churn | Synthetic; no NVIDIA TPOT demonstration; not proof of issue's reported hardware cause | Document exact parameters, scripts, and controlled GPU serving trace |
| Nov 3 | Preemption + prefix cache | CPU synthetic scenario reported 240 → 112 estimated recomputed tokens (~53% fewer); 27 preemption tests reported passing | Estimated computation, NOT throughput/TTFT result | Reproduce and instrument exact tokens, steps and GPU timings |
| Late Oct – early Nov | BlockPool/cache lookup microbenchmark | One discussion reported 8,192 → 33 lookups and ~3.08 ms → ~0.015 ms in a small CPU lookup comparison | Unreproduced microbenchmark; does not establish production inference gain | Add benchmark script, baseline version, warmups and repeat results |
| Oct | GQA / serving throughput comparisons | Discussions reported some ~9–11% decode tok/s differences in a particular setup | Unreproduced in this repo; not automatically a general GQA win | Record model, platform, code, prompts, seeds and repeatable results |
| Nov 3 | DeepSeek V4.1 tokenizer/API HTTP 400 fix | [PR #65366](https://github.com/vllm-project/vllm/pull/65366) reports 53 combined CPU test passes; no normal full-model serving run | Real open PR; tests described in PR; not GPU/infra benchmark | Upstream CI/review and complete limitations |

## Evidence hygiene

1. **Concept understood** ≠ code path verified.
2. **AI-assisted code/test pass** ≠ independent design skill proved.
3. **CPU simulation** ≠ NVIDIA GPU TTFT/TPOT result. Real professional GPU experiments *were performed* per user report, but are not public benchmarks.
4. **Issue author's benchmark** ≠ our benchmark.
5. **Open PR** ≠ merged change.

Keep client names, model endpoints, private weights, credentials, and internal benchmark datasets out of this public repository. When importing a safe experiment, capture: commit, hardware, vLLM version, model, commands, request mix, repeat count, raw output, correctness checks, and known confounders. Only then consider Level 5 for that specific measured skill.
