---
description: Spawn a background agent to run a task in parallel while the user keeps working — it stays alive and accessible for the user to open, read, and interact with; the main thread gets only a short launch/finish note, never the agent's report. Pass a plain task, or another slash command (e.g. /oc:subagent /oc:walk-through 247 -r) to run that command in the background.
argument-hint: [a task to run in the background, OR a slash command with its arguments]
---

Spawn a **background agent** to carry out a task **in parallel** so I don't have to wait — kick it off, tell me it's running, and hand control straight back to me.

What I gave you: $ARGUMENTS

If `$ARGUMENTS` is empty, ask me what the background agent should do and stop.

## What the agent should do

Look at `$ARGUMENTS`:

- **If it starts with a slash command** (e.g. `/oc:walk-through 247 -r`, `/oc:save`) → the agent's job is to **run that command** with the arguments that follow it. Have the agent invoke the command through its own `Skill` tool (the `oc:*` commands are registered as skills — `/oc:walk-through` → skill `oc:walk-through`, args `247 -r`). If for some reason it can't invoke the command that way, tell it to locate the command's definition and follow it. Pass the arguments through **verbatim**, including any flags.
- **Otherwise** → the agent's job is the **plain task** described in `$ARGUMENTS`. Give it the task as-is.

## How to launch it

- Use the **Agent** tool with a **general-purpose** agent — always general-purpose, whatever the task.
- Launch it to run **in the background** so I keep working; don't block on it. Give it a short, descriptive label naming the task — that label is how I'll find it, so make it clear.
- After launching, tell me in **one line** what it's doing and that it's running in the background — and that I can **open the subagent to read its report or interact with it**. Then **return control to me**. Don't narrate its progress.
- I may fire off several of these — each `/oc:subagent` is its own independent agent. Don't try to coordinate them.

## Keep it alive so I can navigate to it

The single hard requirement: the agent must **stay alive and accessible** so I can open it, read its report, and message it **directly** for follow-ups — not through you. Everything else is secondary to that.

- **Never stop, kill, or cancel the agent.** Don't call any stop/cancel on it, don't let a chained command tear it down, and don't do anything that would discard it. It lives until I'm done with it. (Its process may idle out on its own after a while, but its transcript persists and stays resumable — that's fine; what matters is I can always navigate to it.)
- **Launch it truly in the background** — async, non-blocking. Don't await it and don't consume-and-discard its result to "finish" it.

## When it finishes — a short note is fine, never the content

A brief completion notification is welcome; a report is not.

- **When it completes, at most one short line** — e.g. `bg task "<label>" finished — open it to read`. **Never** paste, summarize, or quote its result into this thread, on completion or when I ask. If I ask whether it's done, a bare "still running" / "finished — open it" is the whole answer.
- **Store the output only if the task asked for it.** If `$ARGUMENTS` says to save the output (e.g. "…and save it", "…write it down"), have the agent push it to the odeck MCP server with `push_summary`. Set `title` to the task name, `session_id` to `$CLAUDE_CODE_SESSION_ID`, `provider` to `claude-code`, and `tags` to include `bg`. If the server is not connected, it falls back to `.octo-stack/bg/<slug>.md` (`<slug>` a short kebab-case name for the task). That's the agent's doing, not a report from you.
- If the agent ran a chained slash command that stores its own output (like `/oc:walk-through` in auto mode), that stored summary is the output — I'll read it or open the subagent. Don't echo it here.
