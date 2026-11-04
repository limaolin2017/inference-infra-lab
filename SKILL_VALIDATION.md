# Skill Validation — L1–L4 + A/H methods

**Started:** 2026-11-04. This file records *formal validation*, not everything studied. See [SKILL_MAP.md](SKILL_MAP.md) for the knowledge inventory, and [CURRENT.md](CURRENT.md) for the next task.

## Two axes, not six levels

| Skill depth | Pass condition |
| --- | --- |
| **L1 Explain** | Explain what happens, why, and one limit without hints |
| **L2 Apply** | Solve an unseen case; predict what changes with different conditions |
| **L3 Trace** | Locate current source code, name key functions, explain two state transitions, predict a changed path |
| **L4 Engineer** | Define an invariant, design a meaningful test, judge its outcome, and explain a safe fix and limits — **regardless of who runs the test** |

**L1–L4 = what you can do. A/H = how your work was verified.** In both A and H, *you* design the key check and the pass/fail criteria. In **A**, an Agent performs the execution under your direction; in **H**, you do the work hands-on. Neither tag is an extra level. The same L4 skill can be recorded as **L4-A** and/or **L4-H**.

## Verification method A — User-directed Agent verification

**A means: I design the verification and direct the Agent to run it.** It does *not* mean the Agent invented and approved its own verification.

Before execution, **the learner**, not Codex, must specify:

1. **Claim:** exactly what behavior should be true.
2. **Cases:** at least one ordinary case, boundary case, and negative/control case where practical.
3. **Pass/fail rule:** observable expected outputs or assertions, set **before** seeing results.
4. **Execution plan:** environment, commands, evidence to return, and stop condition.
5. **Review:** personally compare results to rules, identify false positives/limits, and give a verdict.

To record **A-PASS**, keep links to **your prewritten plan + Agent execution/log + your review**. If an Agent independently wrote the strategy and you only clicked approve, mark **Agent-assisted evidence, A NOT VERIFIED**. An A pass proves **your ability to design, direct and judge that validation**, for the specific scope. It does not automatically prove manual debugging ability.

## Verification method H — Hands-on manual verification

**H means: I personally reason, predict, execute/inspect and explain.** Tools and documentation are allowed where the test permits, but answers must not be led by an Agent.

For **L1/L2**, answer three *unseen* prompts: explain, apply, predict a changed case. Grade each **0** wrong, **1** incomplete, **2** correct with reasoning. Pass = **at least 5/6 and no major concept error**. The first blind pass is **H-provisional**. Confirm it with a *different unseen case at least 24 hours later*.

For **L3**, independently locate the relevant functions on a pinned commit, explain two state changes, and predict the effect of a new input before checking. Confirm on a different case later.

For **L4-H**, apply the **same L4 engineering standard** as L4-A, but independently execute or change the test and investigate the result yourself instead of delegating execution. State the expected result before running it. A later changed-case repeat is needed for **H-confirmed**. Clicking Run on an Agent-designed test is not sufficient.

**H verifies your demonstrated ability on sampled cases, not a universal job-readiness percentage.**

## Example: Scheduler preemption

| Step | A: I direct Agent | H: I do it myself |
| --- | --- | --- |
| Goal | I specify expected preemption invariant | I explain it without hints |
| Test | I define workload/KV limits and pass/fail cases before execution | I design a fresh case and predict result |
| Execution | Codex runs my plan | I trace current code and/or run the test myself |
| Decision | I inspect logs against my rules | I compare result with prediction and explain a failure |
| Proof | Prewritten plan + logs + my verdict | First prediction + source/test log + delayed retest |

## Current baseline: no unearned passes

`—` = not formally validated; **reported** = previous conversation or PR description. Historic Agent-written tests are **not** automatically user-directed A passes. Guided explanations are **not** automatically H passes.

| Skill | Earlier evidence | A: I directed Agent | H: hands-on | Next test |
| --- | --- | --- | --- | --- |
| Prefill / decode | Discussed and explained | — | Pending | New L1–L2 blind questions |
| Attention / GQA | Guided discussion | — | Pending | Derive K/V shape and memory |
| Weights vs hidden states | Previous basic concept check | — | Pending formal check | New case and delayed repeat |
| KV Cache / Prefix caching | Guided reasoning | — | Pending | CPU hit/miss prediction |
| BlockPool / `ref_cnt` | [#64281](https://github.com/vllm-project/vllm/pull/64281) reports passing CPU tests | **Pending plan-authorship evidence** | Pending | You design CPU edge-case test, Agent runs it |
| Scheduler / admission / preemption | Chat-reported CPU simulation | **Pending plan-authorship evidence** | Pending | You set policy test and pass rules |
| Model Runner | Discussed, no independent trace | — | Pending | Trace source, predict state changes |
| EAGLE / speculative decoding | Concept checks, guided lookahead | — | Pending formal check (P2) | Optional future blind quiz |
| NVIDIA profiling | Prior professional serving reported | — | GPU access limited | Optional controlled GPU test later |

The formal checks in the existing [2026-11-05 quiz](reviews/2026-11-05.md) are **not attempted yet**. The report of an Agent running tests does not tell us who set their pass criteria.

## Record template (fill BEFORE running)

```text
Skill / L1-L4:
Method: A (I designed and directed Agent) / H (hands-on)
Date:
Claim and invariant:
My test design BEFORE execution (normal / boundary / negative cases):
My expected results and PASS/FAIL rules BEFORE execution:
My prediction:
Agent command + requested logs (A) / My actual manual steps (H):
Real output, code commit, and evidence links:
My comparison with the original rules:
Failure analysis / confounders:
Verdict: PASS / PARTIAL / MISS / NOT ATTEMPTED
Second unseen test ≥24 hours later for H-confirmed:
Next gap:
```

## How to update

1. Select one **P0 core skill**.
2. Write and preserve the test plan or unseen answers **before** feedback or execution.
3. Run the test; inspect actual output and version.
4. Record a precise verdict and the evidence link.
5. For **H-confirmed**, repeat a *different case* at least one day later.
6. Update this tracker only. [SKILL_MAP.md](SKILL_MAP.md) links here instead of copying formal grades.

**Current focus:** Scheduler → KV Cache / BlockPool → Model Runner, with CPU-verifiable upstream Issues. No personal GPU is required for these checks.
