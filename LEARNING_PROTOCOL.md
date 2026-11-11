# Learning Protocol — Issue to Evidence

This file defines **how we work**. For priorities see [ROADMAP.md](ROADMAP.md). For current work see [CURRENT.md](CURRENT.md).

## Non-negotiable learning filter: trunk before leaves

Before introducing a new term, deep Issue or experiment, locate it on the **Request → Scheduler → KV Cache Manager / BlockPool → Model Runner → GPU** path. Ask what job-relevant P0 gap it closes and what check or code trace it will produce. If no clear answer, **park it as P2** instead of opening a research detour.

**One session = one core-path question and one observable outcome.** Prefer a complete understanding of a common case over an exhaustive explanation of a rare edge case. If a side branch blocks the core path, learn only the minimum needed to unblock it. Keep EAGLE/lookahead, full 2Q/ARC research and obscure offloading details optional unless needed for an actual target role or core bug.

See [ROADMAP.md](ROADMAP.md#trunk-first-rule-2026-11-04) for the stable priority gate. Do not create another syllabus or log for this rule.

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

- Explain the behavior with a small example or **SVG diagram / animation**, when a visual helps. Prefer SVG over ASCII.
- Ask one unfamiliar question **without hints**.
- **When stuck (2026-11-09):** draw one picture, shrink the numbers to the smallest case that still shows the idea (e.g. `block_size = 4`), then ask one more question of the same type. Do not add more text first.
- **New concept format (2026-11-09):** one picture + at most 3 short lines + 1 question. Learn a main-path stage as concepts first, then do **one** ~15-minute code check at the end of that stage (not one per concept).
- **Reviews and recaps too (2026-11-09 evening):** same format — pictures first, very little text. Chinese mixed with English is fine; terms stay in English.
- **English practice (2026-11-09 evening):** end each reply with 2–3 simple, reusable English sentences (Chinese gloss in brackets).
- **Start of session (2026-11-10):** read [CURRENT.md](CURRENT.md) first and plan review from [REVIEW_QUEUE.md](REVIEW_QUEUE.md), not from chat memory.
- **Job-relevant first (2026-11-10):** say why a concept matters at work before quizzing; never ask about terms not yet taught; prefer questions an infra engineer meets on the job (e.g. which knob to turn for a complaint).
- **Order and format (2026-11-11):** show the main path / big picture before a stage. Then concept as an **animation** (more animation, fewer words) → learner confirms it is clear → **one clearly stated question at a time** (plain text question + one picture; avoid multi-question interactive quizzes) → code last. Never quiz before the concept has been taught in this session.
- **Learner locates source (2026-11-09):** for source questions, the assistant gives only the file and function name. The learner finds the lines and explains them; the assistant only confirms or corrects. At least once per session. Source the assistant reads and shows counts as **assistant-shown**, never as an independent trace.
- Record learning difficulties in [REVIEW_QUEUE.md](REVIEW_QUEUE.md). Record **formal scores and A/H proof in [SKILL_VALIDATION.md](SKILL_VALIDATION.md)**.
- **A — Human-led, Agent-verified:** the learner defines **what to verify, what to compare, and the acceptance criteria** before execution. Codex may **propose experiment designs, choose supporting test cases, write tests, run them, and collect evidence**. The learner reviews the plan, approves or changes it, and independently judges the results. Merely accepting an Agent verdict without understanding the comparison is not an A pass.
- **H — Hands-on:** the learner independently predicts, traces, runs or changes a test, and explains the outcome. An unseen follow-up check helps confirm retention.
- L1–L4 describe **skill depth**, while A/H describe the **verification method**.

### Review split — short mornings, deeper evenings (2026-11-05; refined 2026-11-09)

**Core principle:** Review is for **finding knowledge gaps**, not for solving every gap immediately. Some ideas need time to settle. Protect the main study session for new P0 learning, source tracing, and experiments; aim for roughly **20% review / 80% new work**, not a strict time ledger.

- **Morning:** hard cap **15 minutes or 3 closed-book P0 questions**, whichever comes first. Stop sooner if the highest-priority gaps are identified.
- **Evening:** optional **10–15 minutes** for remaining due P0 questions. Use fresh, short cases from any earlier day. No new deep topics at night.
- **If independently correct with clear reasoning:** mark the sampled case Pass and move on; avoid repeating the same arithmetic or example. Formal H/A validation still follows [SKILL_VALIDATION.md](SKILL_VALIDATION.md).
- **If incorrect or unsure:** give one brief explanation and, if useful, one quick check. Mark Partial/Miss if the understanding still needs guidance. Record the precise confusion in [REVIEW_QUEUE.md](REVIEW_QUEUE.md) for a fresh check **next day or later**, instead of extending the session.
- When a learner interrupts a question for clarification, answer briefly and return to the **same unanswered question**. If still unclear, park it without requiring an immediate correct answer.
- **Hard stop:** do not let review crowd out the day's main path. Priority: (1) previously wrong/partial P0, (2) not recently checked P0, (3) independently untested P0. EAGLE/P2 remains optional.
- **GitHub timing:** keep notes for an **after-17:30** update by default. An explicit user request may override this for that day.

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

**Batch updates (2026-11-09):** during a session, keep notes in one local draft only. Write to GitHub **once, when the learner says the session is done**, not after every question. The update must cover **the whole session from start to end** (2026-11-10), not only the last part. In that update, record details only for **Partial / Miss** items (journal + REVIEW_QUEUE); a **Correct** check gets one line in the journal.

## 6. Daily learning log (short and factual)

Use **one `journal/YYYY-MM-DD.md` per study day**, not an extra root `LOG.md`. Keep the current-day page easy to scan:

| Field | Record |
| --- | --- |
| **New** | Concepts first learned today; mark **L1/L2/L3/L4 practice depth**, not a pass |
| **Reviewed** | Older concepts revisited; mark **L1–L4 practice depth** and whether help was used |
| **Checks** | Exact questions, learner predictions, result (**Correct / Partial / Miss**), hints, and evidence link |
| **Next** | **One P0 main-path skill** to study, plus the exact missing proof |

**Level rule:** Reuse the existing **L1 Explain, L2 Apply, L3 Trace source, L4 Engineer**. A daily entry like **"L2 practice (guided)"** means an L2-type exercise was attempted; it is **not a skill certification**. Only write **L2-H**, **L3-A**, etc. when its independent / user-directed evidence meets the rules in [SKILL_VALIDATION.md](SKILL_VALIDATION.md). **A** means the learner set the verification goal, comparison, and acceptance criteria, critically reviewed the Agent's suggested experiment, and assessed its evidence; **H** means hands-on independent verification. Do not award A from an unreviewed Agent-designed test or Agent-only verdict.

**Keep formal grades only in [SKILL_VALIDATION.md](SKILL_VALIDATION.md)**, the next focus only in [CURRENT.md](CURRENT.md), and detailed explanations in [concepts/](concepts/). Daily logs stay brief, link to evidence, and do not duplicate complete skill registers.

**Example:** [2026-11-04 daily log](journal/2026-11-04.md).

## 7. Visuals

Prefer **SVG diagrams** over ASCII, and **SVG animation** where movement helps. Prefer SVG over GIF. Ensure GitHub Markdown rendering works. Mark animations as *concept examples*, never as traces or measured experiments.

**Loop:** Target → Current → Gap → Issue → Explanation → Test → Feedback.
