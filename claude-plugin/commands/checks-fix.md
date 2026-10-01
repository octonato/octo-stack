---
description: Run the session checks — comment-reviewer and proof-reader in parallel — then apply every finding through the fixer agent. Add -i/--interactive to pick which findings to apply.
argument-hint: [optional commit hash to widen the scope from that commit (inclusive) to HEAD, on top of the uncommitted changes] [-i|--interactive]
---

Check what changed in this session against my writing rules and **fix it**. This is the **fix** half, like `scalafmt`: by default it applies every finding. `/oc:checks` is the report-only half.

What I gave you: $ARGUMENTS

## Flags

Strip flags out of `$ARGUMENTS` before reading the rest as the scope.

- **`-i` / `--interactive`** — show me the findings and let me pick which to apply. Without it, apply all.

## Scope

- By default, the scope is the **uncommitted changes**: staged, unstaged, and untracked files from `git status`.
- If the remaining `$ARGUMENTS` is a commit hash or git ref, the scope is `<hash>^..HEAD` (the hash is **inclusive**) **plus** the uncommitted changes.

If the scope is empty, say `Nothing to check.` and stop.

## Run the checks

Dispatch both reviewers **in parallel** — in the same message — over the same scope:

1. **`oc:comment-reviewer`** — content of added or edited code comments.
2. **`oc:proof-reader`** — English of added or edited comments and documentation.

Use the **Agent** tool with `subagent_type` set to the agent's name. If the name isn't available, find its definition under the plugin's `agents/` folder and run a **general-purpose** agent with that file's body as the prompt. Tell each agent the scope in one sentence (`uncommitted changes`, or `commit range <hash>^..HEAD plus uncommitted changes`). Don't add instructions of your own.

Wait for both to finish.

## Merge

Build one list from both results:

- Sort by file, then line.
- If both reviewers flag the **same `file:line`**, keep **one** entry. Join the two rules with `;`. If either fix is `delete`, the merged fix is `delete`.
- Tag each entry with its source: `[comment]`, `[prose]`, or `[comment;prose]`.

If the merged list is empty, print `Checks passed.` and stop.

## Pick what to apply

Print the numbered list, one finding per line:

```
1. src/Foo.scala:42 — medium — [comment] explains why — "so that the cache stays warm" → delete
2. docs/setup.md:8 — low — [prose] passive voice — "the config is read by the loader" → "The loader reads the config."
```

- **Default:** apply all. Go straight to the next section.
- **`-i`:** stop and ask me which to apply: **all**, a subset (by number), or **none**. Wait for my answer. If I say none, change nothing and confirm.

## Apply

Hand the chosen findings, **verbatim and numbered**, to **`oc:fixer`** in one Agent call. Don't apply edits yourself and don't reword the findings. Wait for it to finish.

## Verify

Run both reviewers **once more**, in parallel, over the same scope. Merge as before.

Then report:

```
Applied 5, skipped 1.
Remaining: 1 finding.

Skipped:
- src/Foo.scala:42 — fragment not found

Remaining:
1. docs/setup.md:8 — low — [prose] over 20 words — "..." → "..."
```

Drop any empty section. If nothing remains, the last line is `Checks passed.`

Don't loop. One fix pass, one verify pass, then stop. Never commit — that's mine.
