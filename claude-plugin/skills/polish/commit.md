# Polish commit

This procedure commits one phase. The `polish` skill uses it. The state file format is in `state.md`, next to this file.

The caller names the phase, and may give a note for the commit message.

The message file is `.nogit/polish/{slug}.commit-msg`.

## Write the message

Write the commit message to the message file:

- The subject is `polish({IDs}): {what the phase fixes}`, in the imperative, under 72 characters. `{IDs}` lists the phase's finding IDs, comma-separated: `polish(F1):`, or `polish(F2,F5):`.
- The body follows the format and rules below. Add the caller's note at the end, if there is one.
- Add no `Co-Authored-By` line.

```markdown
**Finding {ID}:** {one or two sentences}

**Fix:** {a short paragraph}
```

Rules for the body:

- Do not repeat the commit subject.
- **Finding** states the problem for a reader who has not seen the review. Use one or two sentences.
- **Fix** states what the commit changes. Use a short paragraph.
- Label each **Finding** with its ID, for example `**Finding F1:**`.
- When a phase covers several findings, write one **Finding** paragraph per finding.
- Follow the plain-English rules in my CLAUDE.md.
- Write GitHub Markdown. Put code names in backticks: classes (`AroundClassName`), methods, fields, properties (`property-names`), files and commands. Use a list when the fix has several separate parts.

## Commit

Stage only the files the phase changed. Never stage `.nogit/`.

Commit with `git commit -F {message file}`, then delete the message file. Never edit the message file in this step. Never push.

The header's `mode` and `gpg` fields decide signing:

- `mode: auto`: always commit with `--no-gpg-sign`.
- `gpg: unknown`: commit normally. If the commit fails on signing, commit again with `--no-gpg-sign` and set `gpg: unavailable`. Say in the report that the commits need signing later. If the commit succeeds, set `gpg: available`.
- `gpg: unavailable`: commit with `--no-gpg-sign`. Do not mention it again.
- `gpg: available`: commit normally.

## Update the state file

- Set the phase's `status: committed`, its `commit` to the short hash, and its `subject` to the commit subject.
- Set each of its findings to `status: fixed`, except those already `left out`.

## Report

Report:

- the commit's short hash and subject
- the commit body
- the signing note, if there is one
