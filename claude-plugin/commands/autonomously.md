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

4. **Run my writing checks** once the phase builds, before committing it. See "Closing a phase" below. Never ask me anything during this step.

5. **Commit each phase separately** once the checks are applied:
   - Prefix the message with `phase N: ` (e.g. `phase 1: add config parser`).
   - Make **unsigned** commits — pass `--no-gpg-sign` so signing never blocks you. I'll squash and sign them myself on review.
   - **No `Co-Authored-By: Claude` trailer.**

6. **Advance** to the next phase on your own. Repeat until all phases are done.

## Closing a phase

Dispatch both reviewers **in parallel** — in the same message — over the **uncommitted changes**: staged, unstaged, and untracked files from `git status`.

1. **`oc:comment-reviewer`** — content of added or edited code comments.
2. **`oc:proof-reader`** — English of added or edited comments and documentation.

Use the **Agent** tool with `subagent_type` set to the agent's name. If the name isn't available, find its definition under the plugin's `agents/` folder and run a **general-purpose** agent with that file's body as the prompt. Tell each agent the scope in one sentence (`uncommitted changes`). Don't add instructions of your own.

Wait for both to finish. Merge the results into one list:

- Sort by file, then line.
- If both reviewers flag the **same `file:line`**, keep **one** entry. Join the two rules with `;`. If either fix is `delete`, the merged fix is `delete`.

If the list is empty, commit the phase. Otherwise hand the findings, **verbatim and numbered**, to **`oc:fixer`** in one Agent call. Don't apply edits yourself and don't reword the findings. Wait for it to finish, then commit the phase. Findings the fixer skipped go in the final summary; don't re-run the checks and don't stop for them.

## When to stop

- **On success:** after the final phase, stop and give me a concise summary — the phases you completed, the commits you made (`phase N:` subjects), the findings the fixer skipped, and any FIXMEs or deviations left for me.
- **On a failure you can't resolve:** stop at that phase, leave the work in a clear state, and report what failed and what you tried. Do not push past a blocker or paper over it.
