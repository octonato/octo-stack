# Polish review

This procedure reviews a target and writes the findings to the state file. `polish-start` and `polish-all` both use it. The state file format is in `state.md`, next to this file.

## 1. Resolve the target

The target is one of:

- **A PR number or URL.** Run `gh pr view <pr> --json number,headRefName,baseRefName,headRepository`. The diff is `gh pr diff <pr>`. Check that the current branch is the PR's head branch. If it is not, tell me and stop.
- **A commit hash.** The hash is inclusive: review `<hash>^..HEAD`. If the hash is not in the current repository, look for it in the sibling repositories with `git -C <dir> cat-file -e <hash>^{commit}`. Use the repository that owns it. If none owns it, tell me and stop.
- **A range `a..b`.** Review it as given.
- **Nothing.** Review the current branch against its base. Take the base from `gh pr view --json baseRefName` when the branch has a PR. Otherwise use the remote's default branch. The diff is `git diff <base>...HEAD`.

Find the PR for the branch with `gh pr view --json number` when the target is not a PR. Record `none` if there is no PR.

If the diff is empty, say `Nothing to review.` and stop.

Build the slug as `state.md` describes. If `.nogit/polish/{slug}.md` already exists, ask me whether to resume it or to start over, then stop. To start over, rename the old file to `{slug}.{date}.md` and continue.

## 2. Run the reviewers

Dispatch these five reviewers **in parallel**, in the same message, over the same diff:

1. `pr-review-toolkit:code-reviewer`: bugs, logic errors and project guideline violations
2. `pr-review-toolkit:pr-test-analyzer`: missing tests and weak test coverage
3. `pr-review-toolkit:silent-failure-hunter`: swallowed errors and bad fallbacks
4. `pr-review-toolkit:comment-analyzer`: comments that are wrong, stale or misleading
5. `pr-review-toolkit:type-design-analyzer`: types that fail to express or enforce their invariants

Use the **Agent** tool with `subagent_type` set to the reviewer's name. If a name is not available, run a **general-purpose** agent for it instead. Give that agent the reviewer's focus from the list above as its task.

Tell each reviewer the scope in one sentence: the PR number, or the commit range. Ask each one to return located findings with a severity, a location as `path:line` and a suggested fix. Tell each one not to edit files.

Do not run `code-simplifier`. It edits code.

Wait for all five to finish.

## 3. Merge

- Merge findings that point at the same problem. Keep the clearest wording and list both sources.
- Sort by severity, worst first, then by file and line.
- Number them `F1`, `F2`, and so on in that order. The IDs never change after this step.

## 4. Check the findings

Read the code for every `critical` and `high` finding, and for any other finding you doubt.

- If the code confirms the finding, set `checked: confirmed`.
- If the code does not confirm it, set `status: left out` and add it under **Left out** with the reason `not confirmed: {what the code shows}`.
- If you did not read the code for it, set `checked: not checked`.

Some findings raise a behaviour question that only I can answer. For example: "should a cancelled stream still record the interaction?" Add each one under **Open questions**, linked to its finding, and set the finding's `status: deferred`.

## 5. Write the state file

Create `.nogit/polish/{slug}.md` with the header, the **Findings** section and the **Left out** and **Open questions** entries from step 4. Leave `gpg` as `unknown`. Write the slug to `.nogit/polish/current`.
