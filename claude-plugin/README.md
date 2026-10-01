# oc

A Claude Code plugin with commands for feature workflows, code walk-throughs, GitHub, and writing.

Commands are invoked with the `oc:` prefix — `/oc:walk-through`, `/oc:feat-spec`, and so on.

## Commands

- `/oc:autonomously`: Delegate a task for autonomous execution — work through phases, committing each, without waiting for review
- `/oc:check-inbox`: Check the code for TODO-AI / FIXME-AI / QUESTION-AI comments left for Claude — fix or answer each one, and remove the tag once the user approves
- `/oc:checks`: Run the session checks — comment-reviewer (comment rules) and proof-reader (plain-English rules) in parallel over the changed code — and report located findings. Changes nothing; /oc:checks-fix applies them.
- `/oc:checks-fix`: Run the session checks — comment-reviewer and proof-reader in parallel — then apply every finding through the fixer agent. Add -i/--interactive to pick which findings to apply.
- `/oc:feat-implement`: Implement a slice of the active feature — a phase, the tests, or a named part — per spec & plan
- `/oc:feat-plan`: Interactively author a phased execution plan from the spec — files impacted, sequencing, to .features/{name}/plan.md
- `/oc:feat-spec`: Interactively author a feature spec — research the code, clarify, draft to .features/{name}/spec.md
- `/oc:feat-status`: Show where the active feature stands — spec/plan state, phases, and what's done vs left
- `/oc:gh.issue-create`: Create a GitHub issue from a natural language description. Opens in browser for review before submission.
- `/oc:proceed`: Implement what we discussed in this session, then run comment-reviewer and proof-reader in parallel and apply every finding through the fixer agent.
- `/oc:resolve-conflicts`: Resolve git merge conflicts — apply the clear ones with a brief rationale, ask about the ambiguous ones. Add -i/--interactive to get a report on why the conflicts exist and approve each chunk before it is applied.
- `/oc:retry`: Re-attempt the action the user just rejected, optionally with an adaptation
- `/oc:save-summary`: Summarize the research/analysis from this session into the odeck knowledge base
- `/oc:subagent`: Spawn a background agent to run a task in parallel while the user keeps working — it stays alive and accessible for the user to open, read, and interact with; the main thread gets only a short launch/finish note, never the agent's report. Pass a plain task, or another slash command (e.g. /oc:subagent /oc:walk-through 247 -r) to run that command in the background.
- `/oc:submit-review`: Submit PR feedback as a pending GitHub review with inline comments on the diff — never auto-submits
- `/oc:walk-replay`: Replay a walk-through pushed by /oc:walk-through — present each step verbatim in the terminal, one at a time, advancing when the user says "go ahead". Presents the steps as written; it does not summarize them.
- `/oc:walk-through`: Walk-through of a code path or a set of changes, broken into logical steps. By default it pushes the whole walk to the odeck knowledge base to read later; add -i/--interactive to walk through it one step at a time instead. Add -r/--review to fold in located review findings per step, including the comment and plain-English rule checks.

## Skills

- `/oc:polish-all`: Review the user's own PR and fix it autonomously — one commit per finding, questions held until the end, never pushes
- `/oc:polish-approve`: Commit the phase under review in the active polish review — never pushes
- `/oc:polish-next`: Show and implement the next phase of the active polish review, run its tests, and stop for review
- `/oc:polish-start`: Review a PR, commit or range, check the findings, and plan the fixes as themed phases — the first step of the polish workflow
- `/oc:polish-status`: Show where the active polish review stands — phases, commits and open questions

## Agents

- `comment-reviewer`: Checks every code comment added or edited in a diff — inline, javadoc, scaladoc, docstrings — against the comment rules in CLAUDE.md. Reports located findings; never edits.
- `fixer`: Applies a list of located findings from comment-reviewer and proof-reader — edits comments and prose only, with the smallest change that satisfies the rule. Never touches code, never commits.
- `proof-reader`: Checks the English in prose added or edited in a diff — comments, doc comments, Markdown and other documentation — against the plain-English rules in CLAUDE.md. Reports located findings; never edits.

## Polish workflow

The polish skills review a PR. They turn each finding into a small fix commit.

| Skill | What it does |
|---|---|
| `/oc:polish-start <target>` | Reviews the target, checks the findings and plans the fixes as themed phases. |
| `/oc:polish-next` | Implements the next phase, checks the writing, runs the tests and stops for review. |
| `/oc:polish-approve` | Commits the phase. It never pushes. |
| `/oc:polish-status` | Shows the phases, commits and open questions. |
| `/oc:polish-all [target]` | Reviews, then fixes and commits each finding without stopping. It asks its questions at the end. |

The target is a PR number or URL, a commit hash, a range `a..b`, or nothing for the current branch. A commit hash is inclusive.

The skills keep their state in `.nogit/polish/`. `polish-start` and `polish-all` use the `pr-review-toolkit` reviewers when that plugin is installed.

## Requirements

- [Claude Code](https://docs.claude.com/en/docs/claude-code) installed and working
- The `odeck` MCP server, for the commands that store and read summaries

## The odeck MCP server

`/oc:save-summary`, `/oc:walk-through`, `/oc:walk-replay`, `/oc:subagent` and `/oc:submit-review` keep what they write in the odeck knowledge base, through the `odeck` MCP server. Without it they fall back to files under `.nogit/` in the current working directory.

Build the server from the [octodeck](https://github.com/octonato/octodeck) monorepo and register it with Claude Code:

```
cd mcp && go build -o ~/go/bin/odeck-mcp .
claude mcp add odeck --scope user --env OCTODECK_ROOT=/path/to/summaries -- ~/go/bin/odeck-mcp
```

`OCTODECK_ROOT` is the directory where summaries live, and is required. `OCTODECK_TRIM_PREFIX` is optional. It lists directory prefixes to strip from the working directory when building a summary's project path. Separate entries like `PATH`.

## Install

The plugin is distributed through a Claude Code marketplace. From inside Claude Code:

```
/plugin marketplace add octonato/octodeck
/plugin install oc@octo-cogito
```

The first command registers the marketplace; the second installs the `oc` plugin from it. Run `/plugin` at any time to manage installed plugins.

The marketplace lives at the root of the `octodeck` repository, in `.claude-plugin/marketplace.json`. It points at the `claude-plugin/` folder.

### Moving from the old repository

If you installed the plugin from `octonato/claude-plugin`, remove that marketplace first:

```
/plugin marketplace remove octo-cogito
/plugin marketplace add octonato/octodeck
/plugin install oc@octo-cogito
```

## Install from a local clone (development)

If you are working on the plugin itself, point Claude Code at your local checkout instead:

```
git clone https://github.com/octonato/octodeck
cd octodeck
```

Then, from inside Claude Code:

```
/plugin marketplace add /absolute/path/to/octodeck
/plugin install oc@octo-cogito
```

Claude Code picks up changes under `commands/`, `skills/` and `agents/` the next time it loads the plugin.

## Uninstall

```
/plugin uninstall oc@octo-cogito
```
