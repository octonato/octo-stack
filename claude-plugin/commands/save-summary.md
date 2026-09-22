---
description: Summarize the research/analysis from this session into the odeck knowledge base
argument-hint: [optional: what to summarize, or a title hint]
---

Summarize the research, analysis, or findings from our session into a single Markdown summary, and store it with the **odeck MCP server** (`push_summary`).

I often pull a summary into a **new session in another repo**, so it must be **self-contained**: it has to make sense to a reader (or a fresh Claude) who has *no access to this repo* and *none of our conversation history*. Don't refer to "the file above", "as we discussed", or local paths the other repo won't have — spell out the context, names, and conclusions inline.

## What to capture

- Default to summarizing **the research/analysis we just produced** in this session.
- If `$ARGUMENTS` names or narrows a topic, scope the summary to that.

## How to store it

1. **Derive a title from the content** — short but specific enough to recognize later, e.g. `gRPC retry semantics`, `Auth token refresh analysis`. If `$ARGUMENTS` looks like a title hint, use it. The server slugs the title into the file name.

2. Read the session id: `echo $CLAUDE_CODE_SESSION_ID`.

3. Call `push_summary` with:
   - `title` — from step 1
   - `body` — the full Markdown, shaped as below
   - `session_id` — from step 2
   - `provider` — `claude-code`
   - `summary` — a one-line abstract for list views
   - `tags` — a few free-form topics

4. Don't look for an existing summary first. The server never overwrites: a taken name gets a numeric suffix.

**Fallback.** If the odeck MCP server is not connected, write the summary to `.nogit/<name>.md` instead — `<name>` the kebab-case title, `mkdir -p .nogit` first — and tell me you used the fallback.

## Shape of the summary

- Open with a one- or two-line statement of **what this is and why it exists** (the question that was being investigated). The title lives in the frontmatter, so don't repeat it as an `# H1`.
- Use clear Markdown: headings, bullet lists, fenced code blocks for commands/snippets, tables where they help.
- Embed the **conclusions and the reasoning/evidence** behind them — not just a pointer to where they came from. Include relevant code snippets, command outputs, links, or version/commit references *inline* so the summary stands alone.
- Keep it tight and skimmable. This is a working artifact, not prose.

After pushing, give me a one-line confirmation with the path `push_summary` returned, so I can pull it up later with `get_summary`.
