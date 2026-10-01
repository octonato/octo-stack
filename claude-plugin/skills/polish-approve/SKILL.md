---
name: polish-approve
disable-model-invocation: true
model: sonnet
description: Commit the phase under review in the active polish review — never pushes
argument-hint: [optional note for the commit message]
---

I approve the phase under review. Commit it.

My note for the commit message, if any: $ARGUMENTS

Read `${CLAUDE_PLUGIN_ROOT}/polish/state.md` for the state file format. Then resolve and read the active state file as it describes.

## Check

1. Find the phase with `status: in review`. If there is none, tell me and stop.
2. Check the phase's `tests` result:
   - `passed`: go on.
   - `running`: tell me, wait for the result, then check again.
   - `failed`: tell me what failed and stop.
   - `stale` or `not run`: run the tests, then check again.

## Commit

Read `${CLAUDE_PLUGIN_ROOT}/polish/commit.md` and follow it for the phase under review, with my note.

## Report

Show me the report from `commit.md`.

If I run `/oc:polish-next` without remarks on the commit message, the message is approved. If I ask for changes and the commit is still `HEAD` and not pushed, amend its message.

Then tell me the next step:

- If a phase is still planned, run `/oc:polish-next`.
- Otherwise, run `/oc:polish-next` to go through the open questions, or push the commits when none are open.
