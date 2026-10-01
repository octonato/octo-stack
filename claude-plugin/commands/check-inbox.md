---
description: Check the code for TODO-AI / FIXME-AI / QUESTION-AI comments left for Claude — fix or answer each one, and remove the tag once the user approves
argument-hint: [--all to scan the whole repo instead of just uncommitted files]
---

While coding, I leave you messages in the source as comments tagged **TODO-AI**, **FIXME-AI**, or **QUESTION-AI**. This command means: go read your inbox and deal with each message, one at a time.

- **TODO-AI** — something I want you to implement or change.
- **FIXME-AI** — something broken or wrong that I want you to fix.
- **QUESTION-AI** — something I want you to explain. You answer in the terminal; the code is not changed.

## Scope

By default, scan only the files with **uncommitted changes** (from `git status` — staged, unstaged, and untracked). If `$ARGUMENTS` contains `--all`, grep the **whole repository** instead.

Match the tags as tokens (`TODO-AI`, `FIXME-AI`, `QUESTION-AI`) regardless of comment syntax — `//`, `#`, `<!-- -->`, `/* */` — so it works in any language. If comment lines immediately below a tag line continue the note, treat them as part of the same message.

## First, show me the inbox

Present a short numbered list of everything you found:

```
1. src/foo/Bar.scala:42   TODO-AI      add retry with backoff here
2. src/foo/Baz.scala:17   QUESTION-AI  why does this need a lock?
3. docs/setup.md:8        FIXME-AI     this instruction is outdated
```

If the inbox is empty, say so and stop.

## Then, process one message at a time

Go through them in list order. For each:

- **TODO-AI / FIXME-AI** — propose the code change: show me what you intend to do (the relevant edit, concisely). Then **stop and wait for my approval**. Only after I approve: apply the change and **delete the tag comment**. If I ask questions or request changes, iterate on this message — don't move on until I'm satisfied or I say "skip".
- **QUESTION-AI** — answer the question in the terminal, grounded in the actual code. When I signal I'm satisfied, **delete the comment**. Nothing gets written into the source.
- If I say **"skip"**, leave the tag in place and move to the next message.

**Removal semantics:** delete the whole comment — the tag line plus any continuation comment lines. If the tag is a trailing comment on a code line, remove only the comment part and leave the code intact.

Never remove a tag before I've approved that message. Never batch-apply — one message, one approval.

## Closing

When the inbox is done, give me a short summary: which messages were resolved (with `file:line`), which were skipped and still remain. Do **not** commit anything — that's mine.
