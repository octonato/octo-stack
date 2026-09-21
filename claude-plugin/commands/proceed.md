---
description: Implement what we discussed in this session, then run comment-reviewer and proof-reader in parallel and apply every finding through the fixer agent.
argument-hint: [optional note narrowing or adjusting what to implement]
---

Proceed with the implementation **as discussed in this session**. Then run my writing checks over the result and fix what they find.

What I gave you: $ARGUMENTS

If the session holds no agreed implementation, say so and stop. Don't guess a task.

## Implement

1. Restate in two or three lines what you are about to implement, then start. Don't wait for my approval — the discussion was the approval.
2. If `$ARGUMENTS` is not empty, treat it as an adjustment to the agreed scope.
3. Match the surrounding code's style, naming, and idioms. Keep logical sections separated by blank lines.
4. Keep the build green. Mark temporary or incomplete code with a `FIXME` comment. Build and run the relevant tests when that's quick and available.
5. Never commit — that's mine.

## Run the checks

When the implementation is done, dispatch both reviewers **in parallel** — in the same message — over the **uncommitted changes**: staged, unstaged, and untracked files from `git status`.

1. **`oc:comment-reviewer`** — content of added or edited code comments.
2. **`oc:proof-reader`** — English of added or edited comments and documentation.

Use the **Agent** tool with `subagent_type` set to the agent's name. If the name isn't available, find its definition under the plugin's `agents/` folder and run a **general-purpose** agent with that file's body as the prompt. Tell each agent the scope in one sentence (`uncommitted changes`). Don't add instructions of your own.

Wait for both to finish.

## Merge

Build one list from both results:

- Sort by file, then line.
- If both reviewers flag the **same `file:line`**, keep **one** entry. Join the two rules with `;`. If either fix is `delete`, the merged fix is `delete`.
- Tag each entry with its source: `[comment]`, `[prose]`, or `[comment;prose]`.

If the merged list is empty, skip to the report.

## Fix

Hand the merged findings, **verbatim and numbered**, to **`oc:fixer`** in one Agent call. Don't apply edits yourself and don't reword the findings. Wait for it to finish.

## Report

Give me a short summary:

```
Implemented: <one line per file or logical change>
Checks: applied 5, skipped 1.

Skipped:
- src/Foo.scala:42 — fragment not found
```

Drop any empty section. If the checks found nothing, the last line is `Checks passed.`

Then stop and ask me to review the code. Don't loop, don't re-run the checks, and never commit.
