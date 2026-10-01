---
description: Run the session checks — comment-reviewer (comment rules) and proof-reader (plain-English rules) in parallel over the changed code — and report located findings. Changes nothing; /oc:checks-fix applies them.
argument-hint: [optional commit hash to widen the scope from that commit (inclusive) to HEAD, on top of the uncommitted changes]
---

Check what changed in this session against my writing rules. This is the **check** half, like `scalafmtCheck`: report, don't edit. `/oc:checks-fix` is the other half.

What I gave you: $ARGUMENTS

## Scope

- By default, the scope is the **uncommitted changes**: staged, unstaged, and untracked files from `git status`.
- If `$ARGUMENTS` is a commit hash or git ref, the scope is `<hash>^..HEAD` (the hash is **inclusive**) **plus** the uncommitted changes.

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

## Report

If the merged list is empty, print `Checks passed.` and stop.

Otherwise print the numbered list, one finding per line, in the reviewers' own shape plus the tag:

```
1. src/Foo.scala:42 — medium — [comment] explains why — "so that the cache stays warm" → delete
2. docs/setup.md:8 — low — [prose] passive voice — "the config is read by the loader" → "The loader reads the config."
```

Close with one line: `N findings. Run /oc:checks-fix to apply them.`

Then stop. Don't edit, don't offer to edit, don't summarize the reviewers' transcripts.
