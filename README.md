# octo-stack

Plugins for code assistants that keep the engineer in charge and sharp.

AI agents write more of our code every day. The engineer still owns the result.
To direct agents well, the engineer needs a sharp grasp of the code, the design, and the craft.
That knowledge fades when the agent does all the work.

These plugins hand work to agents, but leave the thinking to the engineer.
Some skills point out problems and let you fix them.
Others walk you through code so you understand it before you change it.

For example:

- `/oc:checks` reports problems in comments and docs. It changes nothing.
- `/oc:resolve-conflicts` applies the clear merge fixes and asks about the ambiguous ones.
- `/oc:walk-through` explains a code path or a change, one step at a time.

## Requirements

- [Claude Code](https://docs.claude.com/en/docs/claude-code) installed and working

## Install

The plugin is distributed through a Claude Code marketplace. From inside Claude Code:

```
/plugin marketplace add octonato/octo-stack
/plugin install oc@octo-stack
```

The first command registers the marketplace; the second installs the `oc` plugin from it. Run `/plugin` at any time to manage installed plugins.

The marketplace lives at the root of the `octo-stack` repository, in `.claude-plugin/marketplace.json`. It points at the `claude-plugin/` folder.

## Install from a local clone (development)

If you are working on the plugin itself, point Claude Code at your local checkout instead:

```
git clone https://github.com/octonato/octo-stack
cd octo-stack
```

Then, from inside Claude Code:

```
/plugin marketplace add /absolute/path/to/octo-stack
/plugin install oc@octo-stack
```

Claude Code picks up changes under `claude-plugin/commands/`, `claude-plugin/skills/` and `claude-plugin/agents/` the next time it loads the plugin.

## Uninstall

```
/plugin uninstall oc@octo-stack
```

## Commands

Commands are invoked with the `oc:` prefix — `/oc:walk-through`, `/oc:polish`, and so on.

| Command | Description |
|---|---|
| `/oc:autonomously` | Delegate a task for autonomous execution — work through phases, committing each, without waiting for review |
| `/oc:check-inbox` | Check the code for TODO-AI / FIXME-AI / QUESTION-AI comments left for Claude — fix or answer each one, and remove the tag once the user approves |
| `/oc:checks` | Run the session checks — comment-reviewer (comment rules) and proof-reader (plain-English rules) in parallel over the changed code — and report located findings. Changes nothing; /oc:checks-fix applies them. |
| `/oc:checks-fix` | Run the session checks — comment-reviewer and proof-reader in parallel — then apply every finding through the fixer agent. Add -i/--interactive to pick which findings to apply. |
| `/oc:gh.issue-create` | Create a GitHub issue from a natural language description. Opens in browser for review before submission. |
| `/oc:proceed` | Implement what we discussed in this session, then run comment-reviewer and proof-reader in parallel and apply every finding through the fixer agent. |
| `/oc:resolve-conflicts` | Resolve git merge conflicts — apply the clear ones with a brief rationale, ask about the ambiguous ones. Add -i/--interactive to get a report on why the conflicts exist and approve each chunk before it is applied. |
| `/oc:retry` | Re-attempt the action the user just rejected, optionally with an adaptation |
| `/oc:save` | Summarize the research/analysis from this session into .octo-stack/ |
| `/oc:subagent` | Spawn a background agent to run a task in parallel while the user keeps working — it stays alive and accessible for the user to open, read, and interact with; the main thread gets only a short launch/finish note, never the agent's report. Pass a plain task, or another slash command (e.g. /oc:subagent /oc:walk-through 247 -r) to run that command in the background. |
| `/oc:submit-review` | Submit PR feedback as a pending GitHub review with inline comments on the diff — never auto-submits |
| `/oc:walk-replay` | Replay a walk-through saved by /oc:walk-through — present each step verbatim in the terminal, one at a time, advancing when the user says "go ahead". Presents the steps as written; it does not summarize them. |
| `/oc:walk-through` | Walk-through of a code path or a set of changes, broken into logical steps. By default it saves the whole walk to .octo-stack/walk-through/ to read later; add -i/--interactive to walk through it one step at a time instead. Add -r/--review to fold in located review findings per step, including the comment and plain-English rule checks. |

## Skills

| Skill | Description |
|---|---|
| `/oc:polish` | Review the user's own PR and fix each finding in its own commit — autonomously by default, or one fix at a time with -i/--interactive. Never pushes. |

## Agents

| Agent | Description |
|---|---|
| `comment-reviewer` | Checks every code comment added or edited in a diff — inline, javadoc, scaladoc, docstrings — against the comment rules in CLAUDE.md. Reports located findings; never edits. |
| `fixer` | Applies a list of located findings from comment-reviewer and proof-reader — edits comments and prose only, with the smallest change that satisfies the rule. Never touches code, never commits. |
| `proof-reader` | Checks the English in prose added or edited in a diff — comments, doc comments, Markdown and other documentation — against the plain-English rules in CLAUDE.md. Reports located findings; never edits. |

## Polish workflow

`/oc:polish` reviews a PR and turns each finding into a small fix commit. It never pushes.

| Call | What it does |
|---|---|
| `/oc:polish [target]` | Reviews, then fixes and commits each finding without stopping. It asks its questions at the end. |
| `/oc:polish -i [target]` | Shows the plan and waits for approval. Then shows each fix with its commit message, and commits it when you approve. |

The target is a PR number or URL, a commit hash, a range `a..b`, or nothing for the current branch. A commit hash is inclusive.

The skill keeps its state in `.octo-stack/polish/`, so an interrupted run can resume. It uses the `pr-review-toolkit` reviewers when that plugin is installed.

## Local storage

The plugin keeps its files in `.octo-stack/`, in the current working directory:

- `.octo-stack/*.md`: summaries saved with `/oc:save`
- `.octo-stack/walk-through/`: walk-throughs
- `.octo-stack/bg/`: output of background agents started with `/oc:subagent`
- `.octo-stack/polish/`: polish review state

These files are for you, not for the project. Add `.octo-stack/` to your global gitignore so no project commits them:

1. Find the file with `git config --global core.excludesFile`. If it prints nothing, Git uses `~/.config/git/ignore`.
2. Add the line `.octo-stack/` to that file.
