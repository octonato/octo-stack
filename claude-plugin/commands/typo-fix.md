---
description: Propose typo fixes for the focused file and apply only the ones I approve
argument-hint: [optional file path, or a passage/line-range to limit the scope]
---

Fix typos in my writing — but only the ones I approve. You never apply changes silently.

Determine the target file:

1. If `$ARGUMENTS` names an existing file path, use that (resolve to absolute relative to cwd).
2. Otherwise read the active focus path from `.nogit/focus` (relative to cwd) and use that file.
3. If neither yields a file, tell me to run `/focus <file>` first, then stop.

If `$ARGUMENTS` is a passage or line-range rather than a path, narrow the scope to that region.

Read the file fresh. Find genuine **typos** only — misspellings, doubled words, wrong-but-real words (their/there, its/it's), stray characters, missing letters. Not style, not grammar (that is `/grammar-fix`).

Then present them as a **numbered list of proposed fixes**, each with location and exact before → after:

```
1. line 14:6  "teh"        → "the"
2. line 22    "the the"    → "the"
3. line 30:1  "definately" → "definitely"
```

Then stop and ask me which to apply: **all**, a subset (by number or line), or **none**. Wait for my answer. Do not edit anything yet.

Once I answer:

- Apply only the fixes I named, using the smallest possible edit per fix.
- Change **only** the typo itself — never adjust surrounding wording, spacing, capitalization, or style. This is my creative text; touch nothing I did not approve.
- If I flagged any item as intentional, skip it.
- After applying, give a one-line summary of exactly what changed (e.g. "Applied 1 and 3; skipped 2").

If I say none, change nothing and confirm.
