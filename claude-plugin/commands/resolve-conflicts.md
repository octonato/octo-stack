---
description: Resolve git merge conflicts — apply the clear ones with a brief rationale, ask about the ambiguous ones. Add -i/--interactive to get a report on why the conflicts exist and approve each chunk before it is applied.
argument-hint: [optional guidance, e.g. which side to prefer or a file to limit the scope] [-i|--interactive]
---

Resolve git merge conflicts in the current repository.

What I gave you: $ARGUMENTS

## Flags

Strip flags out of `$ARGUMENTS` before reading the rest as guidance.

- **`-i` / `--interactive`** — report why the conflicts exist, then walk me through every chunk and wait for my approval before applying it. Without it, apply the clear chunks and ask only about the ambiguous ones.

## Find the conflicts

1. Run `git status` to identify all files with merge conflicts.
2. If there are no conflicts, inform the user and stop.
3. If the guidance names a file, limit the scope to that file.

## Default mode

For each conflicted file:

a. Read the file and locate all conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).
b. For each conflict block, analyze both sides:
   - Understand what each side is doing by reading surrounding context, git log, and related code.
   - Determine if one side is clearly correct (e.g., one side adds new code, the other is unchanged).
   - If confident in the resolution, apply it and explain briefly what you chose and why.
   - If the conflict is ambiguous (both sides make meaningful but incompatible changes), show the user both sides with context and ask which approach to take, or whether to combine them.
c. After resolving all conflicts in a file, remove all conflict markers and stage the file with `git add`.

Then go to **Wrap up**.

## Interactive mode

### Step 1 — Report

Before touching any file, explain why the conflicts exist.

1. Identify the operation and the two sides:
   - Merge: `.git/MERGE_HEAD` exists. `ours` is `HEAD`, `theirs` is `MERGE_HEAD`.
   - Rebase: `.git/rebase-merge` or `.git/rebase-apply` exists. `ours` is the branch being rebased onto, `theirs` is the commit being replayed (`REBASE_HEAD`).
   - Cherry-pick: `.git/CHERRY_PICK_HEAD` exists. `theirs` is that commit.
2. Find the merge base of the two sides with `git merge-base`.
3. For the conflicted files, list the commits on each side since the merge base:
   - `git log --oneline <base>..<theirs> -- <files>`
   - `git log --oneline <base>..<ours> -- <files>`
4. Read the commits on the incoming side. Group them by feature or change. For each group, say what it did and which conflicted files it touched.
5. Do the same for our side.

Print the report in this shape:

```
Operation: merge of origin/main into feature/x
Merge base: a1b2c3d

Incoming (origin/main), 3 commits:
- Extract `RequestParser` into its own file (f00d1e2, 9abc123)
  Touches: src/Server.scala, src/Parser.scala
- Rename `handle` to `serve` (4567def)
  Touches: src/Server.scala

Ours (feature/x), 2 commits:
- Add request timeout (8899aab)
  Touches: src/Server.scala

Conflicts: 2 files, 4 chunks
- src/Server.scala — 3 chunks: both sides changed `handle`; timeout code sits inside the extracted block
- src/Parser.scala — 1 chunk: new file on both sides
```

Wait for me to say go ahead before moving on.

### Step 2 — Walk through each file

For each conflicted file, in the order `git status` lists them:

1. Say which file you are on and how many chunks it has.
2. Give a one-paragraph plan for the file: which side wins where, and what gets combined.

Then, for each chunk in the file, from top to bottom:

1. Show the chunk with a few lines of context on each side. Label the sides with their branch names, not `ours`/`theirs`.
2. Say what each side changed, in one or two sentences each.
3. Show the proposed resolution as a code block.
4. Give the rationale in one or two sentences.
5. Ask me to approve or give input. Wait.
6. If I approve, apply the resolution to that chunk only.
7. If I give input, revise the proposal, show it again, and wait again.
8. Move to the next chunk.

Print each chunk in this shape:

```
src/Server.scala — chunk 2 of 3 (lines 40–58)

<<<<<<< feature/x
  def handle(req: Request): Response =
    withTimeout(config.timeout) { process(req) }
=======
  def serve(req: Request): Response =
    process(req)
>>>>>>> origin/main

feature/x: wraps the body in `withTimeout`.
origin/main: renames `handle` to `serve`.

Proposed:
  def serve(req: Request): Response =
    withTimeout(config.timeout) { process(req) }

Rationale: the changes are independent. Keep the rename and the timeout.

Approve, or tell me what to change.
```

After the last chunk in a file, remove any remaining conflict markers, show the resolved region once, and stage the file with `git add`.

## Wrap up

1. Run `git diff --staged` to show the user a summary of the final resolution.
2. Ask the user if they want to commit or review further. Never commit — that's mine.

## Guidelines

- Prefer combining both changes when they are independent (e.g., two different new imports, two new methods).
- When one side deletes code and the other modifies it, ask the user.
- When both sides modify the same lines differently, ask the user.
- Always explain your reasoning for each resolution so the user can verify.
- Never silently drop changes from either side.
- In interactive mode, never apply a chunk before I approve it.
