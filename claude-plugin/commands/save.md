---
description: Summarize the research/analysis from this session into .nogit/
argument-hint: [optional: what to summarize, or a title hint]
---

Summarize the research, analysis, or findings from our session into one Markdown summary. Write it to `.nogit/` in the current working directory.

I often pull a summary into a **new session in another repo**, so it must be **self-contained**: it has to make sense to a reader (or a fresh Claude) who has *no access to this repo* and *none of our conversation history*. Don't refer to "the file above", "as we discussed", or local paths the other repo won't have — spell out the context, names, and conclusions inline.

## What to capture

- Default to summarizing **the research/analysis we just produced** in this session.
- If `$ARGUMENTS` names or narrows a topic, scope the summary to that.

## How to store it

1. **Derive a title from the content** — short but specific enough to recognize later, e.g. `gRPC retry semantics`, `Auth token refresh analysis`. If `$ARGUMENTS` looks like a title hint, use it.

2. Read the session id: `echo $CLAUDE_CODE_SESSION_ID`.

3. Run `mkdir -p .nogit`. Write the summary to `.nogit/<name>.md`, where `<name>` is the kebab-case title. If the name is taken, add a numeric suffix. Never overwrite.

4. Open the file with YAML frontmatter, then the body shaped as below:
   - `title` — from step 1
   - `date` — today, as `YYYY-MM-DD`
   - `session_id` — from step 2
   - `summary` — a one-line abstract
   - `tags` — a few free-form topics

## Shape of the summary

- Open with a one- or two-line statement of **what this is and why it exists** (the question that was being investigated). The title lives in the frontmatter, so don't repeat it as an `# H1`.
- Use clear Markdown: headings, bullet lists, fenced code blocks for commands/snippets, tables where they help.
- Embed the **conclusions and the reasoning/evidence** behind them — not just a pointer to where they came from. Include relevant code snippets, command outputs, links, or version/commit references *inline* so the summary stands alone.
- Keep it tight and skimmable. This is a working artifact, not prose.

After saving, give me a one-line confirmation with the path of the file you wrote.
