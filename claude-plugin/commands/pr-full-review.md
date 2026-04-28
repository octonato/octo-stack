Perform a full PR review: first understand the PR structure, then provide deep feedback with questions and suggestions for the author.

PR number or first commit (inclusive): $ARGUMENTS

## Process

### Phase 1: Parallel Analysis

Launch **both** agents in parallel:

1. **pr-reviewer agent** — to understand the PR structure, classify changes, and determine review order
2. **pr-feedback agent** — to find bugs, design flaws, and suggest improvements

Pass `$ARGUMENTS` to both agents.

### Phase 2: Combined Report

Once both agents complete, read their output files:
- `.nogit/pr-review-summary.md` (from pr-reviewer)
- `.nogit/pr-feedback.md` (from pr-feedback)

Then present a unified review to the user with these sections:

## PR Overview
Synthesize the core change summary from both reports into a concise explanation of what this PR does and why.

## Change Map
From the pr-reviewer output: the change classification (core logic, supporting, non-essential) and recommended review order. Keep it concise.

## Findings
From the pr-feedback output: bugs, design concerns, and code improvements. Only include sections with real findings.

## Questions & Suggestions for the Author
Merge questions from pr-feedback with any review-order insights that suggest areas of concern. Each item should be actionable and reference specific files/lines. Frame them as a numbered list the user can copy into PR comments.

## Verdict
A clear recommendation: ready to merge, needs minor tweaks, or has blocking issues.
