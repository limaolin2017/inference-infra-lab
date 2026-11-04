# Inference Infra Lab

A public engineering notebook for learning, testing, and contributing to **LLM inference systems**.

**Focus:** vLLM internals, KV cache, scheduling, speculative decoding, performance analysis, and eventually CUDA / GPU kernels.

This repository is not a collection of copied explanations. Each topic should move from **understanding** to **independent explanation**, then to **code and measurement**.

## Start here

- **[CURRENT.md](CURRENT.md)** — current learning state, open questions, and the next session. **Read this first.**
- **[ROADMAP.md](ROADMAP.md)** — skills and engineering gaps.
- **[LEARNING_PROTOCOL.md](LEARNING_PROTOCOL.md)** — the repeatable issue-to-learning workflow and Target → Current → Gap → Action → Feedback loop.
- **[REVIEW_QUEUE.md](REVIEW_QUEUE.md)** — unclear points to retest before new lessons.
- **[Next review quiz](reviews/2026-11-05.md)** — closed-book questions first, no answers in the quiz.
- **[Concepts](concepts/)** — short, testable explanations.
- **[Issues](issues/)** — issue investigations and source references.
- **[Experiments](experiments/)** — reproducible tests and benchmarks.
- **[Journal](journal/)** — compact learning history.
- **[Contributions](contributions/)** — verified upstream work and status.

## Progress means evidence

| Level | Meaning | Evidence |
| --- | --- | --- |
| 0 · Not started | Not studied yet | — |
| 1 · Seen | Recognize the term | Vocabulary notes |
| 2 · Explained | Can explain with a simple example | Written explanation |
| 3 · Verified | Can answer a new question without hints | Self-test / review |
| 4 · Implemented | Can trace and change code, with tests | Commit or test |
| 5 · Measured | Can compare solutions and explain trade-offs | Reproducible benchmark |

**A status applies to one specific skill, not to all of vLLM.** Listening to an explanation is not the same as passing a test.

## Current investigation

[vLLM #65161 — EAGLE + CPU KV offloading](issues/vllm-65161.md)

The first task is to confirm whether its original behavior still reproduces on current vLLM. Related changes [#61589](https://github.com/vllm-project/vllm/pull/61589) and [#64096](https://github.com/vllm-project/vllm/pull/64096) have already merged. No fix is claimed here.

## Working rules

1. Check upstream issue, PR, and current source before writing a fix.
2. Define the problem in simple words.
3. Extract and explain missing concepts.
4. Test understanding before calling a topic "verified."
5. Define one concrete knowledge gap and one engineering gap.
6. Reproduce first. Compare correctness, performance, and cost.
7. Record failures and limitations, not only successful results.
8. Avoid company code, customer data, secrets, and private career details.

_Last learning-state update: 2026-11-04._
