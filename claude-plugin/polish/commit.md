# Polish commit

This procedure commits one phase and queues its PR comment. `polish-approve` and `polish-all` both use it. The state file format is in `state.md`, next to this file.

The caller names the phase, and may give a note for the commit message.

## Commit

Stage only the files the phase changed. Never stage `.nogit/`.

Write the comment text first, following the rules in `state.md`.

Write the commit message:

- The subject is `polish: {what the phase fixes}`, in the imperative, under 72 characters.
- The body is the comment text without its first line, `Claude review: commit {short hash}`. Add the caller's note at the end, if there is one.
- Add no `Co-Authored-By` line.

Write the message to `.nogit/polish/commit-msg`. Commit with `git commit -F .nogit/polish/commit-msg`, then delete the file. Never push.

The header's `mode` and `gpg` fields decide signing:

- `mode: all`: always commit with `--no-gpg-sign`.
- `gpg: unknown`: commit normally. If the commit fails on signing, commit again with `--no-gpg-sign` and set `gpg: unavailable`. Say in the report that the commits need signing later. If the commit succeeds, set `gpg: available`.
- `gpg: unavailable`: commit with `--no-gpg-sign`. Do not mention it again.
- `gpg: available`: commit normally.

## Update the state file

- Set the phase's `status: committed`, its `commit` to the short hash, and its `subject` to the commit subject.
- Set each of its findings to `status: fixed`, except those already `left out`.
- Append a comment entry under **Comments**, in the format `state.md` describes, with `status: pending` and `url: none`.

## Report

Report:

- the commit's short hash and subject
- the comment text, between the markers
- the signing note, if there is one
