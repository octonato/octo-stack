---
name: polish-status
disable-model-invocation: true
model: sonnet
description: Show where the active polish review stands — phases, commits and open questions
argument-hint: [optional slug to inspect, or empty for the active one]
---

Give me a short, read-only status of a polish review. **Don't write anything**: no files, no edits, no commits, no comments.

Read `${CLAUDE_PLUGIN_ROOT}/polish/state.md` for the state file format.

## Resolve the state file

1. If `$ARGUMENTS` names a slug, read `.octo-stack/polish/{slug}.md`.
2. Otherwise resolve the active state file as `state.md` describes.

## Report

Read the state file as it is now. Then show:

- **Target:** the target, the branch, the PR and the mode.
- **Findings:** a count by status: open, fixed, deferred, blocked, left out.
- **Phases:** one line per phase: ID, theme, status, commit and test result. Flag a `stale` test result, and a committed phase whose test base is not the parent of its commit.
- **Open questions:** each open question, with its finding.
- **Environment:** `gpg`, when it is not `unknown`.
- **Next step:** the single most useful thing to do next. For example: "run `/oc:polish-next` for P3", "approve P2 with `/oc:polish-approve`", "push the commits", or "answer Q1 and Q2".

Keep it short. Never change any file from here.
