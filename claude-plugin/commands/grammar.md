---
description: Grammar hints for the focused file, with locations and a brief why — never changes the text
argument-hint: [optional file path, or a passage/line-range to limit the check]
---

Give me grammar hints on my writing. **Do not change the file.** This is a read-only pass — you point, you explain briefly, I decide.

Determine the target file:

1. If `$ARGUMENTS` names an existing file path, use that (resolve to absolute relative to cwd).
2. Otherwise read the active focus path from `.nogit/focus` (relative to cwd) and use that file.
3. If neither yields a file, tell me to run `/focus <file>` first, then stop.

If `$ARGUMENTS` is a passage or a hint rather than a path, narrow the review to that region.

Read the file fresh. Then list grammar issues: subject/verb agreement, tense consistency, pronoun reference, dangling or misplaced modifiers, run-ons and comma splices, parallelism, and punctuation that changes meaning. For each, give the location, the issue, and a one-line **why**:

```
line 14:1 — subject/verb: "the ideas ... is" → plural subject wants "are"
line 16   — comma before "which" reads as restrictive; if the clause is essential, drop the comma
line 23   — tense shift: "walked ... she runs" → keep past or present, not both
```

Rules:

- Locate by line (and column when useful) and quote the smallest fragment.
- One line of *why* per item, no lectures. Name the rule when it helps me learn it.
- This is creative writing. Do **not** flag deliberate fragments, voice, dialect, rhythm, or stylistic choices as errors. If a construction is unusual but might be intentional, raise it as a question, not a correction.
- Do **not** edit and do **not** rewrite my sentences. Suggest grammar, do not rewrite voice. If I'm stuck on how to phrase something, that's `/suggest`. If I want a correction applied, that's `/grammar-fix`.
