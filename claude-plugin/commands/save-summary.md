---
description: Summarize the research/analysis from this session into a self-contained Markdown file under .nogit
argument-hint: [optional: what to summarize, or a filename hint]
---

Summarize the research, analysis, or findings from our session into a single Markdown file under `.nogit/`.

I often carry this file into a **new session in another repo**, so the summary must be **self-contained**: it has to make sense to a reader (or a fresh Claude) who has *no access to this repo* and *none of our conversation history*. Don't refer to "the file above", "as we discussed", or local paths the other repo won't have — spell out the context, names, and conclusions inline.

## What to capture

- Default to summarizing **the research/analysis we just produced** in this session.
- If `$ARGUMENTS` names or narrows a topic, scope the summary to that.

## Where and how to write it

1. **Derive the filename from the content** in **kebab-case** with a `.md` extension — e.g. `grpc-retry-semantics.md`, `auth-token-refresh-analysis.md`. If `$ARGUMENTS` looks like a filename hint, use it (normalize to kebab-case). Keep it short but specific enough to recognize later.

2. Write to **`.nogit/<name>.md`** relative to the current working directory. Create the dir if needed: `mkdir -p .nogit`.

3. If a file with that name already exists, tell me and ask whether to **overwrite**, **pick a new name**, or **append** — don't clobber it silently.

## Shape of the file

- Start with an `# H1 title` and, right under it, a one- or two-line statement of **what this is and why it exists** (the question that was being investigated).
- Use clear Markdown: headings, bullet lists, fenced code blocks for commands/snippets, tables where they help.
- Embed the **conclusions and the reasoning/evidence** behind them — not just a pointer to where they came from. Include relevant code snippets, command outputs, links, or version/commit references *inline* so the file stands alone.
- Keep it tight and skimmable. This is a working artifact, not prose.

After writing, give me a one-line confirmation with the path (`.nogit/<name>.md`) so I can grab it.
