# Learning Protocol — From Issue to Evidence

Use this process for each vLLM / inference-infrastructure issue.

## 1. Inspect the issue first

Record the original report, exact version, expected and actual behavior, hardware, reproduction steps, current status, and any existing fixes.

Never assume the issue is still present on current main.

## 2. Extract the knowledge map

Make a small glossary:
- **Known:** can explain independently.
- **Partly known:** can follow an explanation, but not yet explain a new example.
- **Unknown:** must learn before editing code.

Explain one concept with a concrete example and connect it to what is already known.

## 3. Check understanding

Ask a short, unfamiliar scenario. Require:
- a clear reason for the behavior,
- the important condition or invariant,
- one situation where the rule changes.

Correct misconceptions. Do not mark **Verified** just because an answer sounds familiar.

## 4. Map the goal and the gap

For each issue, distinguish:
- **Knowledge gap:** What concept or data flow is not understood?
- **Engineering gap:** What test, trace, design, or benchmark cannot yet be done without help?
- **Relevance:** Does it develop vLLM core, scheduler, KV cache, or GPU performance skill?

## 5. Engineer in small steps

1. Inspect source and existing tests.
2. Write a failing test or benchmark that reproduces the reported behavior.
3. State the expected invariant.
4. Propose the smallest change.
5. Run correctness tests before performance tests.
6. Record test commands, environment, numbers, and known limits.
7. Search for overlapping PRs before contributing.

## 6. The optimization loop

Treat learning as an iterative, feedback-driven process:

- **Target:** independently understand, test, and improve inference-engine internals.
- **Current:** evidence-backed understanding and engineering skills.
- **Gap:** the smallest important missing skill, not a vague percentage.
- **Action:** one explanation check, source trace, reproduction, or benchmark.
- **Feedback:** evaluate the result and update Current.

This is an **analogy to loss minimization**, not literal gradient descent. There is no computed derivative of an individual's skills. Avoid proxy targets such as PR count alone; prefer reproducible evidence and independent understanding.

## 7. Start with a blind review

At the start of a review session, read [REVIEW_QUEUE.md](REVIEW_QUEUE.md) and the dated quiz. Ask the learner the **questions only**. Do not expose hints, explanations, reference links, or the answer key until the learner has attempted the questions.

After each answer, mark **Pass / Partial / Miss**, explain exactly what was wrong, and add a corrected explanation to the topic note. Retest missed points on a later day with a changed example.

Review scores measure retention for those specific concepts, not job readiness. Keep old misconceptions as learning history; never present them as the learner's current belief after correction.

## 8. Update state

At each session end, change only what the evidence supports:

- `CURRENT.md`: current state and immediate next action.
- `REVIEW_QUEUE.md`: misconceptions and unanswered review questions; do not put answers before the blind attempt.
- `reviews/YYYY-MM-DD.md`: closed-book questions and results after the attempt.
- `issues/<issue>.md`: hypothesis, source links, reproductions, decision.
- `concepts/<topic>.md`: corrected, concise explanation.
- `journal/YYYY-MM-DD.md`: what changed today.
- `experiments/`: executable method and measured result, once available.

**Rule:** no fabricated tests, benchmarks, approvals, or merge status.

## Diagram and animation preference

Use short ASCII diagrams for simple relationships. For motion, use **SMIL SVG animations embedded directly in GitHub Markdown**. Prefer SVG to GIF. Avoid standalone HTML or GitHub Pages unless a task needs real interaction. Keep animations accurate and label concept demos separately from measured traces.

## Suggested issue-note headings

`Problem | Why it matters | Terms | Root-cause hypothesis | Code path | Tests | Measurements | Upstream status | Next decision`
