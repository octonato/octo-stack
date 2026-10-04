---
description: Review a colleague's PR, or the current branch, with /pr-review-toolkit:review-pr and save only the findings to .nogit/. Offers to open them as one PR comment on GitHub, never submits.
argument-hint: [PR number or URL]
---

Review a colleague's PR, or the current branch, and write the findings to `.nogit/`.

PR: $ARGUMENTS

## 1. Resolve the scope

If `$ARGUMENTS` names a PR:

1. Run `gh pr view <pr> --json number,url,headRefName,baseRefName`.
2. Check that the current branch is the PR's head branch.
   If it is not, ask me whether to run `gh pr checkout <pr>`.
   Run it only if I approve and `git status` shows a clean tree. Otherwise stop.

If `$ARGUMENTS` is empty, review the current branch:

1. Run `gh pr view --json number,url,headRefName,baseRefName`.
   It finds the PR of the current branch.
2. If there is no PR, take the base from `git symbolic-ref --short refs/remotes/origin/HEAD`.
   Strip the `origin/` prefix to get `<baseRefName>`.

Then, in both cases:

1. Run `git fetch origin <baseRefName>`.
   The scope is `git diff origin/<baseRefName>...HEAD`.
   If the diff is empty, say `Nothing to review.` and stop.
2. If `git status` shows uncommitted changes, tell me they are not part of the review.
3. Set `<name>` to `pr-<number>` when there is a PR.
   Otherwise, set it to the current branch name with each `/` replaced by `-`.

## 2. Run the review

Invoke the `pr-review-toolkit:review-pr` skill with the Skill tool.
Pass `code comments tests errors types parallel` as its arguments.
Do not pass `simplify`. The code-simplifier agent edits code.

Before you invoke it, state the scope: PR `<number>` if there is one, diff `origin/<baseRefName>...HEAD`.
Tell each review agent to use that diff, to return located findings, and never to edit files.
Tell them to explain each problem and not to propose a fix.

## 3. Check the findings

Read the code for each finding.
Drop a finding when the code does not confirm it.
Merge findings that point at the same problem.

## 4. Write the findings

Write `.nogit/submit-review/<name>.md`.
Run `mkdir -p .nogit/submit-review` first.
If the file exists, rename it to `<name>.<YYYY-MM-DD>.md` before you write.

The file holds the findings and nothing else.
Leave out all of these:

- a title, an intro, or a summary
- the names of the agents or skills that ran
- strengths, praise, or what is solid
- counts, severity totals, or an action plan
- findings the code did not confirm
- fixes, suggested changes, or ` ```suggestion ` blocks

Write each finding in this shape, worst first:

```markdown
### <short title>

`<path>:<line>`

<what is wrong, in one or two sentences>

<why it is a problem: the failure it causes or the rule it breaks>
```

Point out the problem only. Do not say how to fix it.
Separate findings with a blank line.
Write for the PR author. Follow the "How to write" rules in CLAUDE.md.

If no finding survives step 3, write no file. Tell me there are no findings and stop.

## 5. Offer to publish

Give me the path of the file and the number of findings.
If there is no PR, stop here.
Otherwise, ask whether to open it as a PR comment on GitHub.

If I approve:

1. Copy the comment to the clipboard. It starts with the line `:robot: says...`, then a blank line, then the file:
   `{ printf ':robot: says...\n\n'; cat .nogit/submit-review/<name>.md; } | pbcopy`
2. Open the comment form: `gh pr comment <number> --web`.
3. Tell me the comment is on the clipboard, ready to paste.

If the sandbox blocks either command, give me this line to run myself:

```
! { printf ':robot: says...\n\n'; cat .nogit/submit-review/<name>.md; } | pbcopy && gh pr comment <number> --web
```

> [!CAUTION]
> NEVER post the comment. Do not run `gh pr comment` without `--web`. Do not call the GitHub API to create a comment or a review. I review and submit it myself.
