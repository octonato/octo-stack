---
description: Implement a slice of the active feature — a phase, the tests, or a named part — per spec & plan
argument-hint: [phase N | tests [for phase N] | a description of the part you want me to build]
---

I'm delegating a **slice** of the implementation to you. You build only what I hand you; I'm building the rest in parallel. Stay inside your slice and don't touch the parts I'm working on.

The text after the command is: $ARGUMENTS

## Resolve the active feature

1. Read the active feature name from `.features/current` (one-line file at the project root); the directory is `.features/{name}/`. If it's missing, tell me to run `/feat-spec` first, then stop.
2. **Re-read fresh**: `spec.md` and `plan.md` (including my latest inline edits). These define what to build and the phase boundaries. If `plan.md` is missing, tell me to run `/feat-plan` first, then stop.

## Figure out the slice

Interpret `$ARGUMENTS`:

- **`phase N`** — implement that phase exactly as the plan describes: create the listed files, modify the listed files, write the tests.
- **`tests` / `tests for phase N`** — write *only* the tests for that phase (so I can write the implementation against them), per the plan's test notes.
- **a free description** — implement that specific part.
- **empty** — ask me which phase or part you should take, and stop.

Before writing code, restate in one or two lines what you're about to implement and which files it touches, so I can catch a mismatch early. If the slice overlaps files I'm likely editing, say so and confirm the boundary.

## While implementing

- Follow the plan's file list. If reality forces a deviation, note it and tell me rather than silently diverging.
- Match the surrounding code's style, naming, and idioms.
- If you must add temporary or incomplete code to keep the build green, mark it with a `FIXME` comment — per how I work in phases.
- Keep code comments concise; doc comments state the contract (what/why for the caller), not the internals.
- **Do not commit.** I commit, always.

## When done

Build/compile and run the relevant tests if that's quick and available. Then stop and **ask me to review** — summarize what you changed (files + the gist), call out any FIXMEs or deviations from the plan, and wait. Don't advance to another phase unless I explicitly delegate it for autonomous execution.
