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

## Plugins

| Plugin | Assistant | What it holds |
|---|---|---|
| [oc](claude-plugin/) | Claude Code | Commands for feature workflows, code walk-throughs, GitHub, and writing |
