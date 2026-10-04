---
description: Replay a walk-through saved by /oc:walk-through — present each step verbatim in the terminal, one at a time, advancing when the user says "go ahead". Presents the steps as written; it does not summarize them.
argument-hint: [a walk-through slug (e.g. pr-247), OR a file path]
---

I want you to **replay a walk-through** that `/oc:walk-through` saved. Present the pre-written steps to me **one at a time**, exactly as they were written. Do **not** summarize, condense, or rephrase a step — I wrote these on purpose and I want to read them **as-is**. You are the pager here, not the author.

What I gave you: $ARGUMENTS

## Find the walk

A walk is one file, `.nogit/walk-through/{slug}.md`. Resolve `$ARGUMENTS` to one:

- **A slug** (e.g. `pr-247`, `commit-a1b2c3d`, `login-auth-flow`) → read `.nogit/walk-through/{slug}.md`.
- **A file path** (anything with a `/`) → read that file.
- **Empty** → list the files in `.nogit/walk-through/`. If there's exactly one, use it. If there are several, show them and ask which; stop until I pick. If there are none, tell me there's nothing to replay and stop.

If nothing resolves, or the file has no `## Step` sections, say so and stop — don't invent steps.

## Know the running order

The body after the frontmatter is the script. Split it on its `## ` headings:

- Everything **before the first `## Step`** heading is the overview.
- Each **`## Step NN — <title>`** section is one step, in numeric `NN` order (`Step 01`, `Step 02`, … `Step 10`) — sort by the number, not lexically.

That ordered list is the walk. Note how many steps there are so you can mark progress.

## Present the overview, then stop

Print the **overview verbatim** — the mode/target line, the summary, the itinerary, and any review roll-up. Don't add commentary before it and don't rewrite it; just show me what's there.

Then, under it, add one short line: how many steps there are and that I should say **"go ahead"** / **"next"** to see Step 1. Stop.

## Present each step, then stop

When I say **"go ahead"** / **"next"** (or similar), print the **next step section in full, verbatim** — title, pointers, explanation, and its `⚠ Review notes` block if it has one — exactly as written. No summary, no paraphrase, no "in short". If you want to help me open a pointer, that's fine, but the step's own text must appear unaltered.

**One exception: the file paths in the code pointers.** The steps were written with paths relative to the folder they were captured in, and I sometimes run the replay from a **parent folder** (or another spot) where those relative paths no longer resolve. So you **may rewrite the path in a `path:line` pointer** to whatever is valid from my current working directory — keep the line number and everything else identical. This is the only thing you may change; the prose, titles, and explanations stay verbatim. If a path already resolves as-is, leave it untouched.

Close each step with a single navigational line — e.g. `Step 2 of 5 — 3 to go` — and stop. That marker is the only thing you add; it is not a summary of the step.

## How the replay proceeds

- When I **ask a question** about the current step, answer it — read the code the step points at if that helps — but **stay on this step** and don't advance. Answering a question is fine; replacing the step's text with your own summary is not.
- When I say **"go ahead"** / **"next"**, present the next step and stop.
- If I ask to **jump** (back to a step, or ahead to step N), print that step verbatim and continue from there.
- Keep track of where we are. If I lose the thread, restate the ordered step list with the current one marked.

## Closing

After the last step, stop. If I want a recap, I'll ask — this command **replays** what was pushed, it doesn't rewrite it.
