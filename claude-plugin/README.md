# oc

A Claude Code plugin with personal commands for feature workflows, code walk-throughs, GitHub, and writing.

Commands are invoked with the `oc:` prefix — `/oc:walk-through`, `/oc:feat-spec`, and so on.

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
/plugin marketplace add octonato/claude-plugin
/plugin install oc@octo-cogito
```

The first command registers the marketplace; the second installs the `oc` plugin from it. Run `/plugin` at any time to manage installed plugins.

## Install from a local clone (development)

If you are working on the plugin itself, point Claude Code at your local checkout instead:

```
git clone https://github.com/octonato/claude-plugin
cd claude-plugin
```

Then, from inside Claude Code:

```
/plugin marketplace add /absolute/path/to/claude-plugin
/plugin install oc@octo-cogito
```

Changes to files under `commands/` are picked up the next time Claude Code loads the plugin.

## Uninstall

```
/plugin uninstall oc@octo-cogito
```
