# octo

A Claude Code plugin with personal commands and agents for GitHub workflows, PR review, and conflict resolution.

## Requirements

- [Claude Code](https://docs.claude.com/en/docs/claude-code) installed and working

## Install

The plugin is distributed through a Claude Code marketplace. From inside Claude Code:

```
/plugin marketplace add octonato/ai-assist-plugins
/plugin install octo@ai-assist-plugins
```

The first command registers the marketplace; the second installs the `octo` plugin from it. Run `/plugin` at any time to manage installed plugins.

## Install from a local clone (development)

If you are working on the plugin itself, point Claude Code at your local checkout instead:

```
git clone https://github.com/octonato/ai-assist-plugins.git
cd ai-assist-plugins
```

Then, from inside Claude Code:

```
/plugin marketplace add /absolute/path/to/ai-assist-plugins
/plugin install octo@ai-assist-plugins
```

Changes to files under `agents/` and `commands/` are picked up the next time Claude Code loads the plugin.

## Uninstall

```
/plugin uninstall octo@ai-assist-plugins
```
