---
description: Review a colleague's PR with /pr-review-toolkit:review-pr and save only the change requests to .octo-stack/. Offers to open them as a PR comment on GitHub, never submits.
argument-hint: <PR number or URL>
---

Review a colleague's PR and write the change requests to `.octo-stack/`.

PR: $ARGUMENTS

## 1. Resolve the PR

1. If `$ARGUMENTS` is empty, tell me and stop.
2. Run `gh pr view <pr> --json number,url,headRefName,baseRefName,headRefOid`.
3. Check that the current branch is the PR's head branch.
   If it is not, ask me whether to run `gh pr checkout <pr>`.
   Run it only if I approve and `git status` shows a clean tree. Otherwise stop.
4. Run `git fetch origin <baseRefName>`.
   The scope is `git diff origin/<baseRefName>...HEAD`.
   If the diff is empty, say `Nothing to review.` and stop.

## 2. Run the review

Invoke the `pr-review-toolkit:review-pr` skill with the Skill tool.
Pass `code comments tests errors types parallel` as its arguments.
Do not pass `simplify`. The code-simplifier agent edits code.

Before you invoke it, state the scope: PR `<number>`, diff `origin/<baseRefName>...HEAD`.
Tell each review agent to use that diff, to return located findings, and never to edit files.

## 3. Check the findings

Read the code for each finding.
Drop a finding when the code does not confirm it.
Merge findings that point at the same problem.

## 4. Write the change requests

Write `.octo-stack/code-review-pr/pr-<number>.md`.
Run `mkdir -p .octo-stack/code-review-pr` first.
If the file exists, rename it to `pr-<number>.<YYYY-MM-DD>.md` before you write.

The file holds the change requests and nothing else.
Leave out all of these:

- a title, an intro, or a summary
- the names of the agents or skills that ran
- strengths, praise, or what is solid
- counts, severity totals, or an action plan
- findings the code did not confirm

Write each change request in this shape, worst first:

```markdown
### <short title>

`<path>:<line>`

<what is wrong, in one or two sentences>

<the requested change>
```

Use a ` ```suggestion ` block when the fix is a concrete code change.
Separate change requests with a blank line.
Write for the PR author. Follow the "How to write" rules in CLAUDE.md.

If no finding survives step 3, write no file. Tell me there are no change requests and stop.

## 5. Offer to publish

Give me the path of the file and the number of change requests.
Ask whether to open it as a PR comment on GitHub.

If I approve:

1. Copy the comment to the clipboard. It starts with the line `:robot: says...`, then a blank line, then the file:
   `{ printf ':robot: says...\n\n'; cat .octo-stack/code-review-pr/pr-<number>.md; } | pbcopy`
2. Open the comment form: `gh pr comment <number> --web`.
3. Tell me the comment is on the clipboard, ready to paste.

If the sandbox blocks either command, give me this line to run myself:

```
! { printf ':robot: says...\n\n'; cat .octo-stack/code-review-pr/pr-<number>.md; } | pbcopy && gh pr comment <number> --web
```

> [!CAUTION]
> NEVER post the comment. Do not run `gh pr comment` without `--web`. Do not call the GitHub API to create a comment or a review. I review and submit it myself.
