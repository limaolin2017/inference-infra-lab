# Skill Validation — L1–L4 + A/H methods

**Started:** 2026-11-04. This file records *formal validation*, not everything studied. See [SKILL_MAP.md](SKILL_MAP.md) for the knowledge inventory, and [CURRENT.md](CURRENT.md) for the next task.

## Two axes, not six levels

| Skill depth | Pass condition |
| --- | --- |
| **L1 Explain** | Explain what happens, why, and one limit without hints |
| **L2 Apply** | Solve an unseen case; predict what changes with different conditions |
| **L3 Trace** | Locate current source code, name key functions, explain two state transitions, predict a changed path |
| **L4 Engineer** | Define the expected behavior, select a meaningful comparison and acceptance rule, critically assess a test plan, judge its results, and explain a safe fix and limits — **even when the Agent designs or runs the test** |

**L1–L4 = what you can do. A/H = how your work was verified.** In **A**, you define what to verify, what to compare, and what counts as success. An Agent may propose an experiment, design tests, and perform execution. In **H**, you independently reason and do the hands-on work. Neither tag is an extra level. The same L4 skill can be recorded as **L4-A** and/or **L4-H**.

## Verification method A — User-directed Agent verification

**A means: human-led, Agent-verified.** The Agent **may help design experiments**; the learner owns the purpose, comparison, and acceptance decision.

Before execution, the learner must make these explicit, in their own words:

1. **Claim:** what behavior or hypothesis is being checked.
2. **Comparison:** which scenarios, inputs, outputs, or baselines need comparing, and why they answer the question.
3. **Acceptance criteria:** what observable result would pass or fail; choose criteria **before** seeing results.
4. **Plan review:** ask Codex to propose test cases, code, setup, controls, and commands; review or adjust the plan for a meaningful and fair comparison.
5. **Result review:** inspect actual evidence, check confounders or limits, and give **your own** verdict.

The Agent can suggest baselines, boundary cases, or even draft acceptance criteria. The learner must understand, explicitly select, and own the final comparison and pass/fail rules before execution.

For **A-PASS**, save **human-defined goal/comparison/acceptance criteria + reviewed Agent test plan + execution/logs + human verdict**. An Agent-designed test **does not disqualify** A. But a plan that the learner merely rubber-stamps without understanding the comparison, or an unreviewed Agent verdict, remains **Agent-assisted evidence, A NOT VERIFIED**. An A pass verifies ability to direct and judge validation for the given scope. It does not prove independent manual debugging.

## Verification method H — Hands-on manual verification

**H means: I personally reason, predict, execute/inspect and explain.** Tools and documentation are allowed where the test permits, but answers must not be led by an Agent.

For **L1/L2**, answer three *unseen* prompts: explain, apply, predict a changed case. Grade each **0** wrong, **1** incomplete, **2** correct with reasoning. Pass = **at least 5/6 and no major concept error**. The first blind pass is **H-provisional**. Confirm it with a *different unseen case at least 24 hours later*.

For **L3**, independently locate the relevant functions on a pinned commit, explain two state changes, and predict the effect of a new input before checking. Confirm on a different case later.

For **L4-H**, apply the **same L4 engineering standard** as L4-A, but independently execute or change the test and investigate the result yourself instead of delegating execution. State the expected result before running it. A later changed-case repeat is needed for **H-confirmed**. Clicking Run on an Agent-designed test is not sufficient.

**H verifies your demonstrated ability on sampled cases, not a universal job-readiness percentage.**

## Example: Scheduler preemption

| Step | A: I direct Agent | H: I do it myself |
| --- | --- | --- |
| Goal | I define the expected behavior and comparison | I explain it without hints |
| Test | I set pass/fail rules; Codex can propose workloads and test design | I design a fresh case and predict result |
| Execution | Codex builds and runs the reviewed plan | I trace current code and/or run the test myself |
| Decision | I inspect logs against my rules | I compare result with prediction and explain a failure |
| Proof | My comparison/criteria + reviewed plan + logs + my verdict | First prediction + source/test log + delayed retest |

## Current baseline: no unearned passes

`—` = not formally validated; **reported** = previous conversation or PR description. Historic Agent-written tests are **not** automatically A passes without evidence that the learner owned the comparison and acceptance decision. Guided explanations are **not** automatically H passes.

| Skill | Earlier evidence | A: I directed Agent | H: hands-on | Next test |
| --- | --- | --- | --- | --- |
| Prefill / decode | Discussed and explained | — | Pending | New L1–L2 blind questions |
| Attention / GQA | Guided discussion | — | Pending | Derive K/V shape and memory |
| Weights vs hidden states | Previous basic concept check | — | Pending formal check | New case and delayed repeat |
| KV Cache / Prefix caching | Guided reasoning | — | Pending | CPU hit/miss prediction |
| BlockPool / `ref_cnt` | [#64281](https://github.com/vllm-project/vllm/pull/64281) reports passing CPU tests | **Pending human comparison/acceptance evidence** | Pending | You define edge-case comparison and acceptance; Agent may design/run the test |
| Scheduler / token budget / admission / preemption | Token Budget basics checked in guided 2026-11-04 Q&A; prior CPU simulations reported | **Pending plan-authorship evidence** | Formal H pending; simple guided concept checks passed | Independently predict one unfamiliar `schedule()` branch; trace source and test |
| Model Runner | Discussed, no independent trace | — | Pending | Trace source, predict state changes |
| EAGLE / speculative decoding | Concept checks, guided lookahead | — | Pending formal check (P2) | Optional future blind quiz |
| NVIDIA profiling | Prior professional serving reported | — | GPU access limited | Optional controlled GPU test later |

The formal checks in the existing [2026-11-05 quiz](reviews/2026-11-05.md) are **not attempted yet**. The report of an Agent running tests does not tell us who chose the comparisons and acceptance criteria.

## Record template (fill BEFORE running)

```text
Skill / L1-L4:
Method: A (I designed and directed Agent) / H (hands-on)
Date:
Claim and invariant:
My comparison / baseline / claim BEFORE execution:
My expected results and PASS/FAIL rules BEFORE execution:
Agent-proposed test cases and my review/changes:
My prediction:
Agent plan + commands + logs (A) / My actual manual steps (H):
Real output, code commit, and evidence links:
My comparison with the original rules:
Failure analysis / confounders:
Verdict: PASS / PARTIAL / MISS / NOT ATTEMPTED
Second unseen test ≥24 hours later for H-confirmed:
Next gap:
```

## 2026-11-04 evening practice checkpoint — no formal pass

The learner answered four short review prompts ([daily log](journal/2026-11-04.md#evening-review--four-question-check-2026-11-04)). Result: **2 Pass, 1 Partial, 1 Miss**.

- **Scheduler limits:** Q1 gave `4,2` where the asked order was requests, then tokens. The correct values are **2 requests**, **4 tokens total**. Partial. **Independent L2 Apply not demonstrated**.
- **KV full with free token budget:** Q2 correct basic reason (“KV cache is full”). Pass at concept level; no code-path proof.
- **Speculative draft/verify:** Q3 correct basic explanation; **L1 Explain shown on this question**, not an L2 unseen-case pass.
- **EAGLE hidden states vs KV:** Q4 “不知道”; **L1 recall gap**, despite earlier guided practice.
- **`Scheduler.schedule()`:** remains **L1 practice (guided)**; no independent trace.

This was not the prescribed 3-prompt (explain/apply/predict), reasoned 5/6 blind H check; hint status is unknown and there is no delayed repeat. **Do not mark any L1-H, L2-H, A-PASS, or H-confirmed** based on it. Prior practice labels stay historical practice depth, not mastery certification.

## How to update

**Keep this lightweight.** The validation system supports learning; it must not become a second full-time project. Use one real main-path task and record evidence that naturally comes from it. Do not create tests merely to fill cells in this table.

1. Select one **P0 core skill**.
2. For A, record the human-owned claim, comparison and acceptance criteria and the reviewed Agent plan before execution; for H, record unseen answers before feedback.
3. Run the test; inspect actual output and version.
4. Record a precise verdict and the evidence link.
5. For **H-confirmed**, repeat a *different case* at least one day later.
6. Update this tracker only. [SKILL_MAP.md](SKILL_MAP.md) links here instead of copying formal grades.

**Current focus:** Scheduler → KV Cache / BlockPool → Model Runner, with CPU-verifiable upstream Issues. No personal GPU is required for these checks.
