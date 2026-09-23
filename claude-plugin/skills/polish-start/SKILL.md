---
name: polish-start
disable-model-invocation: true
description: Review a PR, commit or range, check the findings, and plan the fixes as themed phases — the first step of the polish workflow
argument-hint: [PR number or URL | commit hash (inclusive) | range a..b | empty for the current branch]
---

Review my changes and plan the fixes. This is the first step of the polish workflow:

1. `/oc:polish-start`: review and plan
2. `/oc:polish-next`: implement the next phase
3. `/oc:polish-approve`: commit the phase and queue its PR comment
4. `/oc:polish-publish`: post the comments after I push

The target is: $ARGUMENTS

Read these two files before you start:

- `${CLAUDE_PLUGIN_ROOT}/polish/state.md`: the state file format
- `${CLAUDE_PLUGIN_ROOT}/polish/review.md`: the review procedure

## Review

Follow `review.md` from start to end. Set `mode: step` in the state file header.

## Plan

Group the findings with `status: open` into phases, and write them under **Plan**.

- A phase is one theme, for example "failure reporting" or "stale comments". It can hold one finding or several.
- Order the phases by severity. The phase with the worst finding comes first.
- Put comment fixes, design clean-ups and missing tests in phases of their own.
- Number the phases `P1`, `P2`, and so on. Set each one to `status: planned`, `commit: none`, `subject: none` and `tests: not run`.

Some findings are not worth fixing in this PR. Set them to `status: left out` and add them under **Left out** with a reason.

Some fixes are mechanical refactors, such as renames and moves. List them under **IDE refactors** for me to do in the IDE. Set their findings to `status: left out` with the reason `IDE refactor`.

## Present the plan

Show me:

- the findings, one line each: ID, severity, location, title, and whether you checked it
- the phases, each with its theme and findings
- the findings you left out, with the reason
- the IDE refactors
- the open questions, if any

Then stop and wait. I will approve the plan or change it. When I change it, update the state file and show the plan again.

Do not ask the open questions now. They wait until all phases are done.

When I approve the plan, tell me to run `/oc:polish-next`. Do not start a phase from here.
