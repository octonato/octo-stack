# Polish state file

The `polish-*` commands keep all their state for one review in a single Markdown file. A new session reads this file to pick up the work.

## Location

- The state file is `.octo-stack/polish/{slug}.md` in the repository that owns the target.
- `.octo-stack/polish/current` holds the slug of the active review, on one line.
- The slug is the branch topic in kebab case, for example `retry-backoff` for `feature/retry-backoff`:
  - For a PR, use the PR's head branch. For any other target, use the current branch.
  - Take the last `/` segment of the branch name. Lowercase it, and replace every run of other characters than letters and digits with `-`.
  - On `main`, `master` or a detached `HEAD`, build the topic from the PR title or the newest commit subject instead. Keep it to four words at most.

Every command resolves the state file the same way:

1. Read `.octo-stack/polish/current`. The state file is `.octo-stack/polish/{slug}.md`.
2. If `current` is missing, list the files under `.octo-stack/polish/` so I can pick one, then stop. If the folder is missing, tell me to run `/oc:polish-start` or `/oc:polish-all`, then stop.

Re-read the state file at the start of every command. I may have edited it by hand.

## Layout

The file has these sections, in this order. Keep the field names exactly as shown. Commands find entries by their headings and field names.

```markdown
# Polish: {target}

- target: {PR #5726 | 6e77038^..HEAD | 6e77038..a1b2c3d | feature/foo vs main}
- repo: {absolute path of the repository}
- branch: {local branch}
- remote: {remote}/{branch}
- pr: {number | none}
- mode: {step | all}
- gpg: {unknown | available | unavailable}

## Findings

### F1: {short title}
- severity: {critical | high | medium | low}
- location: {path:line}
- source: {reviewer name}
- checked: {confirmed | not checked}
- status: {open | left out | deferred | blocked | fixed}

{The problem, in one to three sentences.}

**Suggested fix:** {one short paragraph}

## Plan

### P1: {theme}
- findings: {F1, F3}
- status: {planned | in review | committed}
- commit: {short hash | none}
- subject: {commit subject | none}
- tests: {command} @ {base short hash} → {passed | failed | running | stale | not run}

## Left out

- F7: {reason}

## IDE refactors

- {rename, move or extract, with a location}

## Open questions

- Q1 (F4): {question} — {open | answered: the answer}
```

Leave a section empty when it has no entries. Keep its heading.

## Statuses

A finding's `status` means:

- `open`: planned and not fixed yet
- `left out`: not planned, with a reason under **Left out**
- `deferred`: waiting on an open question
- `blocked`: a fix was tried and failed, and its changes were discarded
- `fixed`: committed

A phase's `status` means:

- `planned`: not started
- `in review`: implemented and waiting for my approval
- `committed`: committed

A phase's `tests` field names the test command and the base: `HEAD` when the run started. The result means:

- `running`: the run is in the background
- `passed` or `failed`: the run finished
- `stale`: the phase's code changed after the run started
- `not run`: no run yet
