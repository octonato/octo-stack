# oc

A Claude Code plugin with personal commands for feature workflows, code walk-throughs, GitHub, and writing.

Commands are invoked with the `oc:` prefix — `/oc:walk-through`, `/oc:feat-spec`, and so on.

## Polish workflow

The polish skills review a PR. They turn each finding into a small fix commit with its own PR comment.

| Skill | What it does |
|---|---|
| `/oc:polish-start <target>` | Reviews the target, checks the findings and plans the fixes as themed phases. |
| `/oc:polish-next` | Implements the next phase, checks the writing, runs the tests and stops for review. |
| `/oc:polish-approve` | Commits the phase and queues its PR comment. It never pushes. |
| `/oc:polish-publish` | After a push, matches each comment to its commit, proof-reads the comments and posts them. |
| `/oc:polish-status` | Shows the phases, commits, comments and open questions. |
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
