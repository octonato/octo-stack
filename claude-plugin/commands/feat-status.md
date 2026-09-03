---
description: Show where the active feature stands — spec/plan state, phases, and what's done vs left
argument-hint: [optional feature name to inspect, or empty for the active one]
---

Give me a quick, read-only status of a feature. **Don't write anything** — no files, no edits. Just orient me.

## Resolve which feature

1. If `$ARGUMENTS` names a feature, inspect `.features/{that-name}/`.
2. Otherwise read `.features/current` (one-line file at the project root) for the active name.
3. If neither resolves, list the subdirectories under `.features/` (if any) so I can pick, then stop. If `.features/` doesn't exist, tell me to start with `/feat-spec`.

## Read fresh and report

Look at the feature directory's files (`spec.md`, `plan.md`) as they are right now. Then give me a compact status:

- **Feature** — name, and the one-line summary from the spec.
- **Stage** — where we are: `spec drafting` → `spec ready` → `plan drafting` → `plan ready` → `implementing`. Infer it from which files exist and whether they still carry open questions or unresolved `[#…]` markers.
- **Open threads** — any unanswered `[#comment]`/`[#question]`/`[#note]`/`[#clarify]` markers or **Open questions** entries in spec/plan, listed with their location so I can jump to them.
- **Phases** (if `plan.md` exists) — the phase list, each with a rough state (not started / in progress / done) inferred by checking whether its listed files exist and look implemented. Be honest that this is an inference, not a guarantee.
- **Next step** — the single most useful thing to do next (e.g. "answer the 2 open questions in spec.md", "run `/feat-plan`", "implement phase 3 — phases 2 and 3 are parallelizable").

Keep it short and scannable. End by pointing at the next step. Never modify any file from here.
