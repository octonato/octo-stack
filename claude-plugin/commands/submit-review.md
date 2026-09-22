---
description: Submit PR feedback as a pending GitHub review with inline comments on the diff — never auto-submits
argument-hint: <PR number>
---

Submit the PR feedback as a **pending** GitHub review with inline comments on the diff.

PR number: $ARGUMENTS

Steps:
1. Gather the review findings, taking the first source that has them:
   - The odeck knowledge base — call `list_summaries`, take the most recent entry tagged `pr-<number>`, and read it with `get_summary`. A walk-through pushed with `-r`/`--review` carries its findings this way.
   - `.nogit/pr-feedback.md`, when odeck holds nothing for the PR or the server is not connected.
   - The current conversation, when neither has anything.
2. Get the PR head commit SHA via `gh api repos/{owner}/{repo}/pulls/{pr_number} --jq '.head.sha'`.
3. Get the full diff via `git diff <merge-base>...HEAD` to identify the correct file paths and line numbers for each comment.
4. Build a JSON payload with:
   - `commit_id`: the head SHA
   - `body`: a short summary of the review (optional)
   - `comments`: array of objects, each with `path` (file path relative to repo root), `line` (line number in the new file), and `body` (the comment text in markdown)
   - Do NOT include an `event` field — omitting it defaults to PENDING so the user can review before submitting.
5. Use GitHub suggestion syntax (` ```suggestion ` blocks) in comment bodies where a concrete code fix is proposed.
6. Write the payload to a temp file and submit via: `gh api repos/{owner}/{repo}/pulls/{pr_number}/reviews --method POST --input <payload_file>`
7. Tell the user the review is pending with a count of comments, and that they can review and submit it in the browser.

Important:
- Use a Python script to build the JSON payload — multi-line comment bodies with code suggestions are hard to handle inline in shell.
- Each comment's `line` must refer to a line in the **new version** of the file as shown in the diff.
- Only include comments that are actionable: bugs, security issues, design questions, concrete improvement suggestions.
