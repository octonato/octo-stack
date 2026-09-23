---
name: polish-publish
disable-model-invocation: true
model: sonnet
description: After I push, match each queued polish comment to its commit on the remote branch, proof-read the comments, and post them to the PR
argument-hint: [optional PR number, when the state file has none]
---

I pushed the polish commits. Post their queued comments to the PR.

The PR, if the state file has none: $ARGUMENTS

Read `${CLAUDE_PLUGIN_ROOT}/polish/state.md` for the state file format. Then resolve and read the active state file as it describes.

## Collect

1. Take every comment entry with `status: pending`. If there is none, say `Nothing to publish.` and stop.
2. Take the PR from the header's `pr` field, or from `$ARGUMENTS`. If there is neither, run `gh pr view --json number` on the branch. If that finds no PR, tell me and stop. Record the PR in the header.

The `gh` field in the header tracks the sandbox:

- `unknown`: run `gh auth status`. If it fails with a network or keychain error, run it again outside the sandbox. Set `gh: sandboxed` or `gh: unsandboxed`, and tell me once when it needs to run outside the sandbox.
- `sandboxed` or `unsandboxed`: run every `gh` command that way. Do not tell me again.

## Match each commit

Run `git fetch {remote} {branch}` with the header's `remote` field. Then, for each pending entry:

1. Run `git branch -r --contains {commit}`. If the list holds the header's `remote`, the hash is valid.
2. If it does not, I rebased or squashed. Look for the entry's `subject` in `git log --format='%h %s' {base}..{remote}/{branch}`, where `{base}` is the merge base with the PR's base branch.
   - With exactly one exact match, use that hash.
   - With no match or several, stop matching this entry. Collect it for step 3.
3. Ask me about the collected entries in one message. For each one, show the entry's phase, old hash and subject, and the candidate commits. I will give a hash, or tell you to skip the entry.

When a hash changes, update the entry's `commit` and the phase's `commit`. Update the first line of the comment text to `Claude review: commit {new hash}`.

## Proof-read

Dispatch `oc:proof-reader` with the **Agent** tool. Tell it the scope in one sentence: the state file's path, with `<!-- comment -->` and `<!-- /comment -->` as the markers, in entries with `status: pending`. Don't add instructions of your own.

If it returns findings, hand them to `oc:fixer` in one Agent call, verbatim and numbered. Don't apply the edits yourself. Keep the findings it skipped for the next step.

## Show and confirm

Show me each pending comment, in phase order:

- the phase ID and the final commit hash
- the comment text, between the markers

Then list the proof-reader findings the fixer skipped. Ask me two things:

1. Post these comments?
2. Post a summary comment that links to each one?

Stop and wait. When I ask for changes, edit the entries in the state file and show them again.

## Post

Before posting, read the PR's comments with `gh pr view {pr} --json comments`. An entry whose first line, `Claude review: commit {hash}`, is already on the PR was posted before. Set its `status: posted`, record its URL, and skip it.

Post the other entries one at a time, in phase order. For each one:

1. Write the text between the markers to a temporary file. Leave out the markers.
2. Run `gh pr comment {pr} --body-file {file}`. It prints the comment's URL.
3. Right away, set the entry's `status: posted`, its `url` to the URL, and its phase's `status: posted`. Write the state file before you post the next entry.

If a post fails, stop. Report which entries were posted and which are still pending. A second run of `/oc:polish-publish` resumes from the pending ones.

When I asked for a summary comment and the header has `summary: none`, post it after all entries. It has one line per posted entry: a link to the comment, the short hash in backticks, and the commit subject. Record its URL in the header's `summary` field.

## Report

Show me the number of comments posted, each one's URL, and the summary comment's URL. List any entry I skipped.
