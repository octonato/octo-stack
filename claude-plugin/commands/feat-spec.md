---
description: Interactively author a feature spec — research the code, clarify, draft to .features/{name}/spec.md
argument-hint: [task description for a new feature, or empty to resume the active one]
---

We are authoring a feature **spec** together, interactively, **through the file**. You research and draft; I read, comment inline, or rewrite, then ask you to re-read. You never treat the spec as finished — I do.
I will add comments using the following format: [#comment], [#question], [#note] or [#clarify]

The text after the command is: $ARGUMENTS

## Resolve the active feature

1. **No `$ARGUMENTS`** → resume the active feature: read the feature name from `.features/current` (a one-line file at the project root); the directory is `.features/{name}/`. If that file is missing, ask me for a task description and stop.
2. **`$ARGUMENTS` given:**
   - If a feature is already active **and** `$ARGUMENTS` reads as answers/guidance for the current spec (not a brand-new task), treat it as refinement of the active feature.
   - Otherwise this is a **new feature**. Determine the name: if I offered an explicit `kebab-case` name (a leading single token), use it; otherwise generate a **short** kebab-case name from the task description. Tell me the name you chose in one line, then proceed.
3. For a new feature, create `.features/{name}/` at the project root (cwd). Use my raw words as the seed for the first `spec.md` draft — don't persist them separately.
4. Record the active feature by writing just the name (no trailing newline) to `.features/current`: `printf %s '<name>' > .features/current`. Also ensure `.features/current` is gitignored (it's personal active-state); add it to `.gitignore` if it isn't already.

## Each time you run

**Re-read fresh** before doing anything: any existing `spec.md` (including my inline comments and rewrites), and `plan.md` if it exists. The files are the source of truth — I may have changed them since you last looked. Scan for my `[#comment]`, `[#question]`, `[#note]`, `[#clarify]` markers: address each one, and remove the marker once it's resolved (for `[#question]`/`[#clarify]`, answer me in chat if it needs discussion rather than guessing).

Then do **source-code research** to ground the spec in reality: use the **Explore** agent (and Grep/Read) to find the relevant modules, current behavior, types, and integration points. Cite concrete `path:line` references in the spec so it's anchored to the actual code.

Decide:

- **If the task is unclear or underspecified** — ask me your clarifying questions *first*, before drafting. Keep them sharp and few. Don't write a speculative spec to paper over ambiguity.
- **If it's clear enough** — draft (or revise) `.features/{name}/spec.md`.

## Spec shape

Write `spec.md` with these sections (drop any that genuinely don't apply, add ones that help):

- **Feature** — the name and a one-paragraph summary of what we're building and why.
- **Context** — current behavior and the relevant code you found, with `path:line` references.
- **Requirements** — what the feature must do, as concrete, checkable statements.
- **Out of scope** — what we are deliberately *not* doing.
- **Open questions** — anything still unresolved. This is our conversation surface in the file; I'll answer inline.
- **Acceptance criteria** — how we'll know it's done, observable and testable.

## Hand the pen back

After writing, give me a one-line summary of what changed and point me at the open questions. Then stop — I'll read, edit or comment in the file, and re-run `/feat-spec` so you fold in my changes. When the spec feels settled, suggest moving to `/feat-plan`. Never write the plan from here.
