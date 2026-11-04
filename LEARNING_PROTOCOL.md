# Learning Protocol — Issue to Evidence

This file defines **how we work**. For priorities see [ROADMAP.md](ROADMAP.md). For current work see [CURRENT.md](CURRENT.md).

## 1. Start with the skill gap

- Which main-path skill are we missing?
- Is this Issue useful for **Scheduler, KV, BlockPool, Model Runner, or serving**?
- Is there a smaller problem that teaches the same skill?
- Can we verify correctness with CPU tests? There is **no GPU currently available**.

Use the [Issue-selection filter](ROADMAP.md#issue-selection-filter). **Do not confuse a difficult Issue with a valuable Issue.**

## 2. Inspect before coding

1. Read the latest Issue, comments and related PRs.
2. Inspect current upstream source and existing tests.
3. Check for a fix or duplicate work.
4. State expected versus actual behavior and the invariant.
5. Timebox exploration; park work that has become a deep side branch.

## 3. Explain, then test understanding

- Explain the behavior with a small example or ASCII diagram.
- Ask one unfamiliar question **without hints**.
- Record learning difficulties in [REVIEW_QUEUE.md](REVIEW_QUEUE.md). Record **formal scores and A/H proof in [SKILL_VALIDATION.md](SKILL_VALIDATION.md)**.
- **A — User-directed Agent:** the learner designs the claim, test cases and pass/fail criteria **before** Codex runs them, then checks the actual logs. If the Agent designed the whole test, A is still pending.
- **H — Hands-on:** the learner independently predicts, traces, runs or changes a test, and explains the outcome. An unseen follow-up check helps confirm retention.
- L1–L4 describe **skill depth**, while A/H describe the **verification method**.

## 4. Produce engineering evidence

1. Reproduce a real failure if possible.
2. Write a failing regression test.
3. Make the smallest safe change.
4. Run related tests and report commands, versions and limits.
5. Review the diff and request upstream feedback only if there is still a gap.

**CPU correctness is valid evidence. CPU timing is not proof of NVIDIA TTFT, TPOT or throughput.** No claim of merged work without checking the PR.

## 5. Record once, in the right place

| What changed? | Where? |
| --- | --- |
| Next action / short status | [CURRENT.md](CURRENT.md) |
| Priority and Issue choices | [ROADMAP.md](ROADMAP.md) |
| Knowledge inventory and gap | [SKILL_MAP.md](SKILL_MAP.md) |
| Formal skill checks, test authorship and A/H verdicts | [SKILL_VALIDATION.md](SKILL_VALIDATION.md) |
| Misunderstanding to review | [REVIEW_QUEUE.md](REVIEW_QUEUE.md) |
| Explanation | [concepts/](concepts/) |
| Root cause, code and decision | [issues/](issues/) |
| Test setup and measurements | [experiments/](experiments/) |
| Open PR / upstream status | [contributions/](contributions/) |
| What happened on a date | [journal/](journal/) |

**One fact should have one main home.** Other files should link to it rather than copy full explanations.

## 6. Daily learning log (short and factual)

Use **one `journal/YYYY-MM-DD.md` per study day**, not an extra root `LOG.md`. Keep the current-day page easy to scan:

| Field | Record |
| --- | --- |
| **New** | Concepts first learned today; mark **L1/L2/L3/L4 practice depth**, not a pass |
| **Reviewed** | Older concepts revisited; mark **L1–L4 practice depth** and whether help was used |
| **Checks** | Exact questions, learner predictions, result (**Correct / Partial / Miss**), hints, and evidence link |
| **Next** | **One P0 main-path skill** to study, plus the exact missing proof |

**Level rule:** Reuse the existing **L1 Explain, L2 Apply, L3 Trace source, L4 Engineer**. A daily entry like **"L2 practice (guided)"** means an L2-type exercise was attempted; it is **not a skill certification**. Only write **L2-H**, **L3-A**, etc. when its independent / user-directed evidence meets the rules in [SKILL_VALIDATION.md](SKILL_VALIDATION.md). **A** means the learner designed and directed the Agent verification; **H** means hands-on independent verification. Do not mark either from a helpful explanation or an Agent-designed test.

**Keep formal grades only in [SKILL_VALIDATION.md](SKILL_VALIDATION.md)**, the next focus only in [CURRENT.md](CURRENT.md), and detailed explanations in [concepts/](concepts/). Daily logs stay brief, link to evidence, and do not duplicate complete skill registers.

**Example:** [2026-11-04 daily log](journal/2026-11-04.md).

## 7. Visuals

Use ASCII for simple structures. Use **SVG with SMIL animation** when movement helps. Prefer SVG over GIF. Mark animations as *concept examples*, never as traces or measured experiments.

**Loop:** Target → Current → Gap → Issue → Explanation → Test → Feedback.
