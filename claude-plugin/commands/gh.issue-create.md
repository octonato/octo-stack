---
description: Create a GitHub issue from a natural language description. Opens in browser for review before submission.
argument-hint: <description of the issue>
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Instructions

1. Read relevant source files if the user references specific code, components, or behavior — enough to write a precise issue.

2. Get the Git remote URL by running:
   ```
   git remote get-url upstream
   ```
   Extract the GitHub `owner/repo` from the URL (e.g. `akka/nexus` from `https://github.com/akka/nexus.git`).

   > [!CAUTION]
   > ONLY PROCEED IF THE REMOTE IS A GITHUB URL. If not, stop and inform the user.

3. Issues should inform and create awareness. The description should not explain or dictate how to fix the issue — it should only describe what needs to be done at a high level.

4. Draft the issue **title** and **body**:
   - Title: concise, under 80 characters, action-oriented (e.g. "Move session tracking to Consumer for at-least-once guarantees")
   - Body structure:
     ```markdown
     ## Problem
     What is wrong or missing, and why it matters.
     ```
   - Present the draft to the user for feedback before creating.

5. Create the issue in **web mode** so it opens in the browser for review:
   ```
   gh issue create --repo <owner/repo> --web --title "<title>" --body "<body>"
   ```

   > [!CAUTION]
   > ALWAYS use `--web` flag. NEVER submit the issue directly. The user will review and submit from the browser.
   > UNDER NO CIRCUMSTANCES EVER CREATE ISSUES IN REPOSITORIES THAT DO NOT MATCH THE REMOTE URL.
