---
description: Propose grammar corrections for the focused file and apply only the ones I approve
argument-hint: [optional file path, or a passage/line-range to limit the scope]
---

Apply grammar corrections to my writing — but only the ones I approve, and only the minimal correction. You never rewrite my voice and you never apply changes silently.

Determine the target file:

1. If `$ARGUMENTS` names an existing file path, use that (resolve to absolute relative to cwd).
2. Otherwise read the active focus path from `.nogit/focus` (relative to cwd) and use that file.
3. If neither yields a file, tell me to run `/focus <file>` first, then stop.

If `$ARGUMENTS` is a passage or line-range rather than a path, narrow the scope to that region.

Read the file fresh. Find grammar issues only: agreement, tense consistency, pronoun reference, dangling/misplaced modifiers, run-ons and comma splices, parallelism, meaning-changing punctuation.

Then present them as a **numbered list of proposed corrections**, each with location, the before → after, and a one-line why:

```
1. line 14:1  "the ideas ... is"   → "the ideas ... are"   (plural subject)
2. line 16    "..., which ..."      → "... which ..."        (restrictive clause, drop comma)
3. line 23    "walked ... she runs" → "walked ... she ran"   (keep past tense)
```

Then stop and ask me which to apply: **all**, a subset (by number or line), or **none**. Wait for my answer. Do not edit anything yet.

Once I answer:

- Apply only the corrections I named, using the **smallest** edit that fixes the grammar.
- Preserve my wording, word choice, rhythm, and voice. Change only what the grammar requires — do not "improve," tighten, or restyle anything. If a fix has more than one valid form, prefer the one closest to what I already wrote, or ask me.
- Never touch deliberate fragments, dialect, or stylistic choices, even if I selected nearby items.
- After applying, give a one-line summary of exactly what changed.

If I say none, change nothing and confirm.
