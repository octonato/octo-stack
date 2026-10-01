---
description: Interactively author a phased execution plan from the spec — files impacted, sequencing, to .octo-stack/features/{name}/plan.md
argument-hint: [optional guidance for the plan, or empty to resume]
---

We are authoring the **execution plan** together, interactively, **through the file**. You draft phases; I read, comment inline, or rewrite, then ask you to re-read. The plan is for **me to implement** — so it must be concrete about which files each phase touches.
I will add comments using the following format: [#comment], [#question], [#note] or [#clarify]. When you re-read, find these, address them, and remove them once resolved.

The text after the command is: $ARGUMENTS

## Resolve the active feature

1. Read the active feature name from `.octo-stack/features/current` (a one-line file); the directory is `.octo-stack/features/{name}/`. If it's missing, tell me to run `/feat-spec` first, then stop.
2. Require `spec.md` in that directory. If it doesn't exist, tell me the spec comes first (`/feat-spec`) and stop.

## Each time you run

**Re-read fresh**: `spec.md` (including my inline edits/comments) and any existing `plan.md`. Re-check the code with **Explore**/Grep/Read where the plan needs to name real files and integration points — don't invent paths.

If `$ARGUMENTS` is non-empty, treat it as my guidance/answers for the plan and apply it.

If anything in the spec is too thin to plan against, ask me — or note it under **Open questions** in the plan and flag it. Don't silently guess.

## Plan shape

Write `.octo-stack/features/{name}/plan.md`:

- **Plan for `{feature}`** — one line, and a pointer to `spec.md`.
- **Phases** — break the work into small, reviewable phases. Number them. For **each phase**:
  - **Goal** — what this phase delivers, in one or two lines.
  - **Files to create** — each new file as `path` + one line on its purpose.
  - **Files to modify** — each existing file as `path` + what changes there.
  - **Tests** — what to test and where the test files live.
  - **Depends on** — which earlier phases must be done first (or "none").
- **Sequencing** — close with an explicit map:
  - which phases are **independent / parallelizable** (can be done at the same time, by me and you splitting them),
  - which are **strictly sequential** and why (the dependency that forces the order).
  Express it as a short dependency list or a small diagram. This is what lets me hand you one phase and take another.

Keep phases sized so each is a sensible review unit — honoring how I work in phases (review between phases, FIXME for any temporary stubs).

## Hand the pen back

After writing, summarize the phase breakdown in a couple of lines and call out the parallelizable phases. Then stop — I'll read, edit or comment in `plan.md`, and re-run `/feat-plan` so you incorporate it. When the plan is settled, remind me I can implement myself, hand you a phase, or split with `/feat-implement`.
