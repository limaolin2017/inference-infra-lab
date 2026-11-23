# CLAUDE.md — Schema for this learning wiki

This repo is a learning wiki for **LLM inference engineering** (vLLM), built on Karpathy's [LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) pattern. The learner curates sources and answers questions. The LLM writes and maintains the wiki pages, the index and the log.

## Layers

| Layer | Where | Who writes | Rule |
| --- | --- | --- | --- |
| 1. Raw sources | [raw/](raw/) | Learner adds | **Read-only.** Never edit after adding. |
| 2. Wiki | [components/](components/) (vLLM parts), [concepts/](concepts/) (ideas), [issues/](issues/), [experiments/](experiments/), [contributions/](contributions/), [assets/](assets/) | LLM | Summaries, concept pages, analyses. Cross-link pages. Flag contradictions. |
| 3. Schema | this file + [LEARNING_PROTOCOL.md](LEARNING_PROTOCOL.md) | Learner + LLM | Change only when the learner agrees. |
| Learner state | [CURRENT.md](CURRENT.md), [REVIEW_QUEUE.md](REVIEW_QUEUE.md), [MISTAKES.md](MISTAKES.md), [SKILL_VALIDATION.md](SKILL_VALIDATION.md), [journal/](journal/) | LLM, from session evidence | Record only what happened. Guided ≠ pass. |
| Navigation | [index.md](index.md), [log.md](log.md) | LLM | Update on every change. |

## Session start

1. Read [CURRENT.md](CURRENT.md) (status board + *Next session*).
2. Read the top section of [REVIEW_QUEUE.md](REVIEW_QUEUE.md) and the latest [journal/](journal/) file.
3. Run `grep "^## \[" log.md | tail -5` to see recent activity.
4. Use [index.md](index.md) to find pages. Do not plan from chat memory.

## Workflows

**Ingest** (a new source: paper, Handbook pages, vLLM file, notes from another tool)
1. Save it, or a pinned link plus a short excerpt, in `raw/`.
2. Discuss the main points with the learner.
3. Update or create the concept pages it touches. Add links both ways.
4. Add it to [index.md](index.md). Append a `log.md` entry.

**Learn / review** (a session)
- Follow [LEARNING_PROTOCOL.md](LEARNING_PROTOCOL.md): place the concept on the main path, say why it matters at work, one picture, one question at a time, count from 0.
- A good answer or a new picture that explains something well goes back into a concept page, not only the chat.

**Close** (when the learner says the session is done, or asks to save)
1. Write `journal/YYYY-MM-DD.md` for the **whole** session.
2. Add misses and partials to the top of [REVIEW_QUEUE.md](REVIEW_QUEUE.md) with a next check, and count them in [MISTAKES.md](MISTAKES.md) (+1 per wrong answer, recount patterns).
3. Update the status board and *Latest session close* in [CURRENT.md](CURRENT.md).
4. Append one `log.md` entry. Update [index.md](index.md) if pages changed.

**Lint** (weekly, or when asked)
- Broken links; pages not in `index.md`; orphan pages with no links in.
- Claims that disagree between pages, or with pinned source.
- Old REVIEW_QUEUE rows that never got a next check.
- Concepts used in the journal that have no concept page.
- Log the result as `lint`.

## Conventions

- `log.md` is append-only. Every entry starts with `## [YYYY-MM-DD] <type> | <title>`. Types: `review`, `learn`, `ingest`, `code-check`, `lint`, `schema`.
- Evidence words: **Right**, **Partial**, **Miss**, **guided**, **own words**, **assistant-shown**. Only [SKILL_VALIDATION.md](SKILL_VALIDATION.md) records formal A/H passes.
- Pin vLLM source links to a commit.
- Simple English. Terms stay in English.
- Every concept and component page starts with **Status** and **Main path** lines.
- Component pages use the same 4 rows: **Owns**, **Decides**, **Answers / Does**, **Called by**. Concept pages link to their component pages and back.
