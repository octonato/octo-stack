---
description: Walk-through of a code path or a set of changes, broken into logical steps. By default it pushes the whole walk to the odeck knowledge base to read later; add -i/--interactive to walk through it one step at a time instead. Add -r/--review to fold in located review findings per step, including the comment and plain-English rule checks.
argument-hint: [a code path/behavior, OR a commit hash, OR a PR number] [-i|--interactive] [-r|--review]
---

I want to **understand code by its logical steps**, not as a wall of text. By default, work autonomously and **write the steps to files** so I can read them at my own pace; with `-i` you instead **guide me through them one at a time** in the terminal.

What I gave you: $ARGUMENTS

If `$ARGUMENTS` is empty, ask me what I want to discover — a code path, a commit hash, or a PR number — and stop. Don't start a walk-through without a target.

## First, decide what we're walking

Look at `$ARGUMENTS` and pick the mode:

- **A commit hash or git ref** (a hex sha like `a1b2c3d`, or something like `HEAD~3`) → **Changes mode.** Walk me through the code changes from that commit through `HEAD`. Per my rules the hash is **inclusive**, so the range is `<hash>^..HEAD` — the parent of the hash through HEAD. Gather the diff with `git diff <hash>^ HEAD` and the commit context with `git log <hash>^..HEAD`.
- **A PR number** (e.g. `247` or `#247`) → **Changes mode.** Walk me through that PR's changes. Get the diff with `gh pr diff <number>` and the context with `gh pr view <number>`.
- **Anything else** (a description of a behavior or code path) → **Code-path mode.** Trace the live flow through the code, as below.

If you can't tell which mode `$ARGUMENTS` means, ask me before doing any research.

## Flags

Strip any flags out of `$ARGUMENTS` before interpreting the rest as the target. Flags may appear in any order and must be passed **separately** — `-i -r`, not bundled as `-ir`. The target itself never starts with a lone `-`, so there's no ambiguity.

- **`-i` / `--interactive`** — turn on **interactive mode**: instead of writing files, walk me through the steps one at a time in the terminal. Without it, the default is **auto mode** (write the steps to files).
- **`-r` / `--review`** — turn on the **review pass** described below. It composes with either mode. It only applies in **Changes mode** — there's nothing to flag as a flaw when we're just understanding existing live code, so in Code-path mode ignore it (mention once that it's a no-op here).

## Then, map the route (quietly)

Before presenting anything, work out the route so every pointer you give me is a real file and line, never invented.

- **Code-path mode:** trace the actual code path with **Explore**/Grep/Read. Follow the real flow — entry point → the calls it makes → where it ends.
- **Changes mode:** read the **full diff** and the commit message(s) / PR description as one body of work — **not commit by commit**. The commits are just how the work was recorded; I want to understand the change by its **logical steps**, which rarely line up with the commit boundaries. Build a model of what the change accomplishes as a whole, then separate the **substantive** changes (the new behavior, the real design decisions) from the **mechanical** ones (renames propagated across call sites, signature changes threaded through, import shuffles, formatting — edits made only to keep the code compiling). Order the substantive changes into a sensible **reading sequence** — the core change first, then what builds on it, tests last — and fold the mechanical churn into a brief mention rather than its own steps. Read the surrounding current code where you need to so each pointer lands on a real, present-day line.

**Do not dump this research on me.** The research is for *you* to plan the route. What I see is the guided walk, not the transcript.

## If `-r`/`--review` is set: gather located findings (Changes mode only)

While you map the route, **in parallel** dispatch the relevant `pr-review-toolkit` specialist agents over the same diff — at least **code-reviewer** and **silent-failure-hunter**, adding **type-design-analyzer** and **pr-test-analyzer** when the change warrants them. Run them as subagents (in the same message, so they run concurrently) and have the route mapping happen alongside, so the review doesn't serialize in front of Step 1.

Give every reviewer the **same output contract**: each finding must come back **located** — `file:line`, a one-line title, a severity, and a sentence of why — so you can slot it into the right step.

In the **same message**, also dispatch my two rule checks over the same diff:

- **`oc:comment-reviewer`** — content of added or edited code comments.
- **`oc:proof-reader`** — English of added or edited comments and documentation.

Use the **Agent** tool with `subagent_type` set to the agent's name. If the name isn't available, find its definition under the plugin's `agents/` folder and run a **general-purpose** agent with that file's body as the prompt. Tell each one the scope in one sentence (`commit range <hash>^..HEAD` or `PR #<number>`). They already answer in a located shape — `file:line — severity — rule — "fragment" → fix` — so no extra contract is needed. If both flag the same `file:line`, keep one finding: join the rules with `;`, and if either fix is `delete`, the fix is `delete`.

Collect the findings, drop duplicates, and **bucket each one under the step whose code it touches** (by file and line). Hold them; don't show me the raw review. They surface inside the walk, per step.

**Do not dump this research on me.** Same rule as the route: I see the guided walk with findings woven in, not the reviewers' transcripts.

## Interactive mode (`-i`): present the itinerary, then stop

**This section and everything below it up to `Auto mode (default)` — the itinerary, the per-step walk, and the closing recap — is the interactive walk and applies only in interactive mode (`-i`). In the default auto mode, skip all of it and jump to `Auto mode (default): write the walk to files`.**

Once you've mapped it, show me a short **numbered list of the steps** — one line each, naming what that step covers. This is the map of where we're going. Then present **Step 1** and stop.

Keep the itinerary tight: enough steps to follow the path (or the change) without skipping anything important, but each step a sensible unit to digest in one read. In changes mode, the steps are **logical units of the change, not commits** — give me a one-line summary of what the change accomplishes as a whole before the step list, so I have the destination in mind, and if there's meaningful mechanical churn, note it in one line so I know it exists without it eating a step.

## Shape of each step

For the current step:

- **Title it** — `Step N: <what this step covers>`.
- **Point me at the code** — give the concrete locations to open, as bare relative paths with line numbers in plain text (e.g. `src/main/scala/foo/Bar.scala:42`), following my citation rules. These are the pointers *I* navigate to. In changes mode, cite the **post-change** line so I open the file at its current state.
- **Walk me through what's there** — explain what this code does and how it connects to the previous step and the next one. In changes mode, explain what the change does, why it's there, and how it serves the overall goal of the PR/commit — not just the mechanics of the diff. A few focused paragraphs at most. This is a guided tour, not a full exposition — surface what matters at this stop and leave the rest for my questions.
- **Surface this step's review notes** (only if `-r`/`--review` is on) — after the explanation, under a short **⚠ Review notes** heading, list the findings bucketed to this step: each as `file:line` + severity + the one-line concern, ordered worst-first. If this step has none, say so in one line. Findings from the rule checks keep their `→ fix`, so I can apply them later with `/oc:checks-fix`. Present these as observations for me to weigh, not edits — this walk explains and flags; it doesn't change code.
- **Hand the pen back** — end by inviting me to ask questions about this step, or to say "go ahead" / "next" to move on. Close with a one-line progress marker showing how many steps remain (e.g. `Step 2 of 5 — 3 to go`).

## How the walk proceeds

- When I **ask a question**, elaborate on the current step — go deeper, read more code if needed — but **stay on this step**. Don't advance.
- When I say **"go ahead"**, **"next"**, or similar, present the **next step** and stop again.
- If I ask to **jump** (back to a step, or ahead), go there.
- Keep track of where we are. If I lose the thread, restate the itinerary with the current step marked.

## Closing

After the final step, give me a short **recap** — the path (or the set of changes) we walked end to end, in the order we saw it, so I leave with the whole shape in my head. If `-r`/`--review` was on, close with a brief **roll-up of the findings** by severity (worst first), each still pointing at its `file:line`, plus a one-line note of any finding that didn't land on a specific step.

## Auto mode (default): push the walk to odeck

This is the **default** — it runs whenever `-i` is **not** passed. You don't walk me interactively; you map the same route (and gather the same `-r`/`--review` findings, if set), then **push the whole walk to the odeck MCP server as one summary** and hand me back a manifest. I read it myself — or replay it with `/oc:walk-replay <slug>` — and come back with questions later.

**The slug.** Derive `{slug}` from the target so different targets don't clash:

- PR number → `pr-<number>` (e.g. `pr-247`).
- Commit hash / ref → `commit-<short-sha>` (e.g. `commit-a1b2c3d`).
- Code path / behavior → a short kebab-case slug of the topic (e.g. `login-auth-flow`).

**One summary per walk.** The overview and every step go in a single `push_summary` call — not one summary per step. Read the session id with `echo $CLAUDE_CODE_SESSION_ID` and call `push_summary` with:

- `title` — `Walk-through {slug}`
- `session_id` — from the env var
- `provider` — `claude-code`
- `summary` — the one-line overall summary
- `tags` — `walk-through`, the `{slug}`, and the mode (`changes` or `code-path`)
- `body` — the overview followed by the steps, shaped as below

**The overview** opens the body, above the first step:

- State the **mode and target** up front — `Commit range \`<hash>^..HEAD\``, `PR #<number>`, or `Code path: <description>`.
- The one-line **overall summary** (in changes mode, what the change accomplishes as a whole; plus the one-line mechanical-churn note if there is any).
- The **numbered itinerary** under a `### Itinerary` heading, one line per step.
- If `-r`/`--review` is on, the **findings roll-up** under a `### ⚠ Findings roll-up` heading, by severity (worst first), each pointing at its `file:line`, plus any finding that didn't land on a specific step.

**One section per step**, each opening with `## Step NN — <title>` — `NN` zero-padded (`Step 01`, `Step 02`, … `Step 10`). `## ` headings mark steps and nothing else, which is how the replay splits the body, so keep every heading inside the overview and inside a step at `###` or deeper. Each section carries the same **Shape of each step** content — the code pointers as bare `path:line`, the guided explanation, and the `⚠ Review notes` block if `-r`/`--review` is on — but **without the interactive tail**: no "hand the pen back", no "go ahead" invitation, no progress marker.

**Re-running a target** pushes a new summary; the server never overwrites and there is nothing to clean up. The replay picks the most recent one.

**Fallback.** If the odeck MCP server is not connected, write the walk to files instead — `.nogit/walk-through/{slug}/00-overview.md` and `.nogit/walk-through/{slug}/step-NN-<slug>.md`, deleting any existing `00-overview.md` and `step-*.md` inside that folder first — and tell me you used the fallback.

**Then report a manifest and stop.** Print the path `push_summary` returned (or the files you wrote), the step count, and each step with its one-line title, plus a one-line reminder that I can page through them with `/oc:walk-replay <slug>`. Say nothing else — keep the mapped route and any findings in context so that when I reload this session and ask about a step, you can answer from where we left off. **Answer follow-up questions in the terminal, on the step I ask about; don't rewrite the summary unless I ask you to.**
