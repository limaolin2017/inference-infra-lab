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
- Record **Pass / Partial / Miss** in [REVIEW_QUEUE.md](REVIEW_QUEUE.md).
- Distinguish guided understanding from independent explanation.

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
| Concept maturity and gap | [SKILL_MAP.md](SKILL_MAP.md) |
| Misunderstanding to review | [REVIEW_QUEUE.md](REVIEW_QUEUE.md) |
| Explanation | [concepts/](concepts/) |
| Root cause, code and decision | [issues/](issues/) |
| Test setup and measurements | [experiments/](experiments/) |
| Open PR / upstream status | [contributions/](contributions/) |
| What happened on a date | [journal/](journal/) |

**One fact should have one main home.** Other files should link to it rather than copy full explanations.

## 6. Visuals

Use ASCII for simple structures. Use **SVG with SMIL animation** when movement helps. Prefer SVG over GIF. Mark animations as *concept examples*, never as traces or measured experiments.

**Loop:** Target → Current → Gap → Issue → Explanation → Test → Feedback.
