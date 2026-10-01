---
name: polish-next
disable-model-invocation: true
description: Show and implement the next phase of the active polish review, run its tests, and stop for review
argument-hint: [optional phase ID, e.g. P3, or empty for the next planned phase]
---

Implement one phase of the active polish review, then stop for my review.

The phase to take is: $ARGUMENTS

Read `${CLAUDE_PLUGIN_ROOT}/polish/state.md` for the state file format. Then resolve and read the active state file as it describes.

## Pick the phase

1. If a phase has `status: in review`, do not start another one. Tell me which phase is waiting, and tell me to approve it with `/oc:polish-approve` or ask for changes. Then stop.
2. If `$ARGUMENTS` names a phase, take it. It must have `status: planned`.
3. Otherwise take the first phase with `status: planned`.
4. If no phase is planned, go to **No phase left** below.

The working tree must be clean, apart from `.octo-stack/`. If it is not, list the changed files and stop. I may be working on them.

## Show the phase

Before writing code, show me:

- the phase ID and theme
- each finding in it: ID, severity, location and title
- the files you expect to change, and the gist of each change

Then start. Do not wait for an answer.

## Implement

- Fix only the findings in this phase. Do not fix other findings on the way.
- Match the style, naming and idioms of the code around the change.
- If you must add temporary or incomplete code to keep the build green, mark it with a `FIXME` comment.
- Some fixes raise a behaviour question that only I can answer. Pick the safer answer, add the question under **Open questions**, and keep going. Do not ask it now.
- If a finding turns out to be wrong, do not fix it. Set its `status: left out`, and add the reason under **Left out**.

## Check the writing

Dispatch both reviewers **in parallel**, in the same message, over the **uncommitted changes**:

1. `oc:comment-reviewer`: content of added or edited code comments
2. `oc:proof-reader`: English of added or edited comments and documentation

Use the **Agent** tool with `subagent_type` set to the agent's name. Tell each agent the scope in one sentence: `uncommitted changes`. Don't add instructions of your own.

Wait for both to finish. Merge the results into one list:

- Sort by file, then line.
- If both reviewers flag the same `file:line`, keep one entry. Join the two rules with `;`. If either fix is `delete`, the merged fix is `delete`.

If the list is not empty, hand the findings to `oc:fixer` in one Agent call, verbatim and numbered. Don't apply the edits yourself. Wait for it to finish. Keep the findings it skipped for the report.

## Test

Run the tests that cover the changed code, and the project's formatter check.

- Record the run in the phase's `tests` field, with `HEAD` as the base.
- Run long test suites in the background, and set the result to `running`.
- If you change the phase's code after a run started, set the result to `stale` and run the tests again.
- When a background run finishes, find its phase by the `tests` field. Record the result there, and tell me.

If the tests fail, fix the cause and run them again. If you cannot fix it, say so, and leave the phase for my review.

## Report

Set the phase to `status: in review`. Then explain the change:

- what changed, per finding, with each location as `path:line`
- the test and formatter results, or that they are still running
- how many writing findings the fixer applied, and each one it skipped
- any `FIXME`, any finding you left out, and any open question you added

Then stop. I will ask questions, ask for changes, or run `/oc:polish-approve`. When I ask for changes, make them, check the writing, test again and report again.

## No phase left

When no phase is planned:

1. If **Open questions** has open entries, ask them one at a time. Record each answer.
   - If an answer needs a code change, add a new planned phase for it, set its findings to `status: open`, and tell me to run `/oc:polish-next`.
   - If an answer needs no change, set the deferred finding to `status: left out` with the answer as the reason.
2. When no question is open, tell me to push the commits.
