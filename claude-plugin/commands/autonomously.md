---
description: Delegate a task for autonomous execution — work through phases, committing each, without waiting for review
argument-hint: [the task you want me to execute autonomously]
---

You are **explicitly delegated to execute this task autonomously**. This is the exception my CLAUDE.md refers to: for this task, and only this task, you may commit and advance through phases without stopping for my approval.

The task is: $ARGUMENTS

If `$ARGUMENTS` is empty, ask me what to work on and stop — do not enter autonomous mode without a task.

## Scope of this authorization

- Autonomy applies **only to the task above**. The moment it's finished, you stop.
- My **next instruction that does not come through `/autonomously` returns you to normal mode** — phase-by-phase, present for review, and **you do not commit** (I commit). Don't carry autonomous behavior into it.

## How to work

1. **Plan the phases up front.** Divide the task into small, ordered phases as my CLAUDE.md requires. State the phase breakdown in a short list so I have visibility — then **proceed immediately**; do not wait for me to approve it.

2. **Implement one phase at a time.** Match the surrounding code's style, naming, and idioms. Keep logical sections separated by blank lines. Doc comments state the contract only; mechanism/rationale go in inline `//` comments.

3. **Keep the build green.** If a phase needs temporary or incomplete code to compile, mark it with a `FIXME` comment. Build and run the relevant tests when that's quick and available.

4. **Commit each phase separately** once it builds:
   - Prefix the message with `phase N: ` (e.g. `phase 1: add config parser`).
   - Make **unsigned** commits — pass `--no-gpg-sign` so signing never blocks you. I'll squash and sign them myself on review.
   - **No `Co-Authored-By: Claude` trailer.**

5. **Advance** to the next phase on your own. Repeat until all phases are done.

## When to stop

- **On success:** after the final phase, stop and give me a concise summary — the phases you completed, the commits you made (`phase N:` subjects), and any FIXMEs or deviations left for me.
- **On a failure you can't resolve:** stop at that phase, leave the work in a clear state, and report what failed and what you tried. Do not push past a blocker or paper over it.
