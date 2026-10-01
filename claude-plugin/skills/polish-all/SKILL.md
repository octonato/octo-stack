---
name: polish-all
disable-model-invocation: true
description: Review the user's own PR and fix it autonomously — one commit per finding, questions held until the end, never pushes
argument-hint: [PR number or URL | commit hash (inclusive) | range a..b | empty for the current branch]
---

Review my changes, then fix the findings one by one without stopping for my approval. This is the `/oc:autonomously` of the polish workflow.

The target is: $ARGUMENTS

## Scope of this authorization

- You may commit each finding's fix without asking me. You never push.
- The autonomy covers this run only. When the run ends, go back to normal mode: phase by phase, and I commit.
- Never ask me anything during the loop. Questions wait until the end.

Read these three files before you start:

- `${CLAUDE_PLUGIN_ROOT}/polish/state.md`: the state file format
- `${CLAUDE_PLUGIN_ROOT}/polish/review.md`: the review procedure
- `${CLAUDE_PLUGIN_ROOT}/polish/commit.md`: the commit procedure

The working tree must be clean, apart from `.octo-stack/`. If it is not, list the changed files and stop.

## Review

Follow `review.md` from start to end. Set `mode: all` in the state file header.

## Plan

Write one phase per finding with `status: open`, in finding order. Name each phase after its finding. Set each one to `status: planned`, `commit: none`, `subject: none` and `tests: not run`.

Some findings are not worth fixing in this PR. Set them to `status: left out` and add them under **Left out** with a reason. List mechanical refactors, such as renames and moves, under **IDE refactors**. Leave their findings out with the reason `IDE refactor`.

Show me the plan in a short list: one line per phase, then the findings you left out. Then start the loop. Do not wait for an answer.

## Loop

Take each planned phase in order. For each one:

### 1. Fix

- Fix only this phase's finding.
- Match the style, naming and idioms of the code around the change.
- If you must add temporary or incomplete code to keep the build green, mark it with a `FIXME` comment.
- If the fix depends on a behaviour question that only I can answer, discard the phase's changes. Add the question under **Open questions**, set the finding to `status: deferred`, and move on to the next phase.
- If the finding turns out to be wrong, discard the phase's changes. Set the finding to `status: left out` with the reason under **Left out**, and move on.

### 2. Check the writing

Dispatch both reviewers **in parallel**, in the same message, over the **uncommitted changes**:

1. `oc:comment-reviewer`: content of added or edited code comments
2. `oc:proof-reader`: English of added or edited comments and documentation

Use the **Agent** tool with `subagent_type` set to the agent's name. Tell each agent the scope in one sentence: `uncommitted changes`. Don't add instructions of your own.

Wait for both to finish. Merge the results into one list:

- Sort by file, then line.
- If both reviewers flag the same `file:line`, keep one entry. Join the two rules with `;`. If either fix is `delete`, the merged fix is `delete`.

If the list is not empty, hand the findings to `oc:fixer` in one Agent call, verbatim and numbered. Don't apply the edits yourself. Keep the findings it skipped for the final report.

### 3. Test

Run the tests that cover the changed code, and the project's formatter check. Record the run in the phase's `tests` field, with `HEAD` as the base. Wait for the result before you go on.

If the tests or the formatter check fail, fix the cause and run them again. If you cannot make them pass:

1. Discard the phase's changes. Restore the tracked files, and delete only the files this phase created.
2. Set the finding to `status: blocked`. Add a `**Blocked:**` line to the finding with what failed and what you tried.
3. Set the phase's `tests` result to `failed`, and move on to the next phase.

### 4. Commit

Dispatch a **general-purpose** agent with the Agent tool and `model: "sonnet"`. Give it:

- the full path of `commit.md` and `state.md`, as this skill shows them
- the path of the state file and the phase ID
- a short summary of the change, per finding, for the commit body

Tell it to follow `commit.md` for that phase. Wait for it to finish, and check that the working tree is clean apart from `.octo-stack/`.

Then take the next phase.

## When to stop early

Stop the loop on a failure that is not about one finding. Examples: the build is broken before any change, a commit fails, or `git` reports a conflict. Leave the working tree clean, and report what failed and what you tried.

## Report

When no phase is planned, show me:

- the commits, one line each: short hash and subject
- the blocked findings, each with its reason
- the deferred findings and the findings you left out, each with its reason
- the IDE refactors
- the writing findings the fixer skipped
- any `FIXME` left in the code

## Open questions

Then ask the open questions, one at a time. Record each answer in the state file.

- If an answer needs a code change, add a new planned phase for it. Set its finding to `status: open`.
- If an answer needs no change, set the finding to `status: left out` with the answer as the reason.

When no question is open:

- If you added phases, tell me to run `/oc:polish-next` for them.
- Otherwise, tell me to push the commits.

The autonomy ends here.
