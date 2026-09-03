# oc

A Claude Code plugin with personal commands for feature workflows, code walk-throughs, GitHub, and writing.

Commands are invoked with the `oc:` prefix — `/oc:walk-through`, `/oc:feat-spec`, and so on.

## Requirements

- [Claude Code](https://docs.claude.com/en/docs/claude-code) installed and working

## Install

The plugin is distributed through a Claude Code marketplace. From inside Claude Code:

```
/plugin marketplace add octonato/claude-plugin
/plugin install oc@claude-plugin
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
/plugin install oc@claude-plugin
```

Changes to files under `commands/` are picked up the next time Claude Code loads the plugin.

## Uninstall

```
/plugin uninstall oc@claude-plugin
```
