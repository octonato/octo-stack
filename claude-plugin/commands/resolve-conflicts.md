---
description: Resolve git merge conflicts — apply the clear ones with a brief rationale, ask about the ambiguous ones
argument-hint: [optional guidance, e.g. which side to prefer or a file to limit the scope]
---

Resolve git merge conflicts in the current repository.

## Instructions

1. Run `git status` to identify all files with merge conflicts.
2. If there are no conflicts, inform the user and stop.
3. For each conflicted file:
   a. Read the file and locate all conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).
   b. For each conflict block, analyze both sides:
      - Understand what each side is doing by reading surrounding context, git log, and related code.
      - Determine if one side is clearly correct (e.g., one side adds new code, the other is unchanged).
      - If confident in the resolution, apply it and explain briefly what you chose and why.
      - If the conflict is ambiguous (both sides make meaningful but incompatible changes), show the user both sides with context and ask which approach to take, or whether to combine them.
   c. After resolving all conflicts in a file, remove all conflict markers and stage the file with `git add`.
4. After all files are resolved, run `git diff --staged` to show the user a summary of the final resolution.
5. Ask the user if they want to commit the merge or review further.

## Guidelines

- Prefer combining both changes when they are independent (e.g., two different new imports, two new methods).
- When one side deletes code and the other modifies it, ask the user.
- When both sides modify the same lines differently, ask the user.
- Always explain your reasoning for each resolution so the user can verify.
- Never silently drop changes from either side.

Arguments: $ARGUMENTS
