---
description: Point out typos in the focused file, with locations — never fixes them
argument-hint: [optional file path, or a passage/line-range to limit the check]
---

Find typos in my writing and show me where they are. **Do not change the file.** This is a read-only pass.

Determine the target file:

1. If `$ARGUMENTS` names an existing file path, use that (resolve to absolute relative to cwd).
2. Otherwise read the active focus path from `.nogit/focus` (relative to cwd) and use that file.
3. If neither yields a file, tell me to run `/focus <file>` first, then stop.

If `$ARGUMENTS` is not a path but looks like a passage or a hint (quoted text, a line range, "the second paragraph"), use it to narrow the check to that region of the focused file.

Read the file fresh — it may have changed since I focused it. Then list only genuine **typos**: misspellings, doubled words, wrong-but-real words (their/there, its/it's), stray characters, missing letters, obvious slips. Do NOT flag style or grammar here — that belongs to `/grammar`.

Format each as one terse, located line:

```
line 14:6 — "teh" → "the"
line 22   — doubled word: "the the"
line 30:1 — "definately" → "definitely"
```

Rules:

- Locate every item by line (and column when it helps). I read in my editor, so coordinates matter.
- Quote the smallest surrounding fragment so I can find it fast.
- If you find none, say so in one line.
- Do **not** edit, and do not offer to edit. Applying fixes is `/typo-fix`, which I'll run if I want them.
- This is my creative work. When something might be a deliberate spelling, coinage, or dialect, flag it gently as "intentional?" rather than asserting it's wrong.
