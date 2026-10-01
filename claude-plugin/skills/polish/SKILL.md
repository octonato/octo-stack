---
name: polish
disable-model-invocation: true
description: Review the user's own PR and fix each finding in its own commit — autonomously by default, or one fix at a time with -i/--interactive. Never pushes.
argument-hint: [-i] [PR number or URL | commit hash (inclusive) | range a..b | empty for the current branch]
---

Review my changes, then fix the findings one by one, one commit per finding.

What I gave you: $ARGUMENTS

## Mode

Strip the flag out of `$ARGUMENTS` before reading the rest as the target.

- **Default (`auto`):** fix and commit each finding without stopping for my approval.
- **`-i` / `--interactive`:** show me the plan and wait for my approval. Then show me each fix with its commit message, and commit only when I approve it.

## Scope of this authorization

- In `auto` mode, you may commit each finding's fix without asking me. In `interactive` mode, commit only what I approve.
- You never push.
- The authorization covers this run only. When the run ends, go back to normal mode: phase by phase, and I commit.
- Never ask me the open questions during the loop. They wait until the end.

Read these three files before you start:

- `${CLAUDE_PLUGIN_ROOT}/skills/polish/state.md`: the state file format
- `${CLAUDE_PLUGIN_ROOT}/skills/polish/review.md`: the review procedure
- `${CLAUDE_PLUGIN_ROOT}/skills/polish/commit.md`: the commit procedure

The working tree must be clean, apart from `.octo-stack/`. If it is not, list the changed files and stop.

## Review

Follow `review.md` from start to end. Set `mode` in the state file header.

When I resume an existing review, skip the review and the plan. Use the mode the header records. Go to the loop and continue from the first phase that is not `committed`. If that phase is `in review`, show it to me again, with the message file as it is, and wait for my answer.

## Plan

Write one phase per finding with `status: open`, in finding order. Name each phase after its finding. Set each one to `status: planned`, `commit: none`, `subject: none` and `tests: not run`.

Some findings are not worth fixing in this PR. Set them to `status: left out` and add them under **Left out** with a reason. List mechanical refactors, such as renames and moves, under **IDE refactors**. Leave their findings out with the reason `IDE refactor`.

Show me the plan in a short list: one line per phase, then the findings you left out.

- In `auto` mode, start the loop. Do not wait for an answer.
- In `interactive` mode, stop and wait. I will approve the plan or change it. When I change it, update the state file and show the plan again. Start the loop when I approve it.

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

If the list is not empty, hand the findings to `oc:fixer` in one Agent call, verbatim and numbered. Don't apply the edits yourself. Keep the findings it skipped for the report.

### 3. Test

Run the tests that cover the changed code, and the project's formatter check. Record the run in the phase's `tests` field, with `HEAD` as the base. Wait for the result before you go on.

If the tests or the formatter check fail, fix the cause and run them again. If you cannot make them pass:

1. Discard the phase's changes. Restore the tracked files, and delete only the files this phase created.
2. Set the finding to `status: blocked`. Add a `**Blocked:**` line to the finding with what failed and what you tried.
3. Set the phase's `tests` result to `failed`, and move on to the next phase.

### 4. Commit

In `auto` mode, dispatch a **general-purpose** agent with the Agent tool and `model: "sonnet"`. Give it:

- the full path of `commit.md` and `state.md`, as this skill shows them
- the path of the state file and the phase ID
- a short summary of the change, per finding, for the commit body

Tell it to follow `commit.md` for that phase: first **Write the message**, then **Commit**. Wait for it to finish, and check that the working tree is clean apart from `.octo-stack/`.

In `interactive` mode, follow **Write the message** in `commit.md` yourself. Set the phase to `status: in review`. Then show me:

- what changed, per finding, with each location as `path:line`
- the test and formatter results
- how many writing findings the fixer applied, and each one it skipped
- the commit message, subject and body, exactly as the message file holds it

Then stop and wait for my answer:

- **"approved", "next", or similar:** follow **Commit** in `commit.md`. Commit the message file as it is. Never rewrite it at this point.
- **Changes to the code:** make them, check the writing, test again, and update the message file if the change affects it. Then show me the phase again.
- **Changes to the message:** edit the message file, and show me the message again.
- **A question:** answer it, and stay on this phase.

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

- If you added phases, run the loop for them in the same mode, then show me the report again.
- Otherwise, tell me to push the commits.

The authorization ends here.
