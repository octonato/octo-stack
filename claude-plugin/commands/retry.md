---
description: Re-attempt the action I just rejected, optionally with an adaptation
argument-hint: [optional change to apply before retrying]
---

I rejected the previous tool call / proposed action, and I want you to re-attempt it now.

The text after the command (if any) is: $ARGUMENTS

- **If that text is empty:** the denial was an accident on my end. Retry the exact action you were about to perform right before it was denied — same tool, same arguments. Don't redesign your approach or ask me to clarify; just re-issue the call.

- **If that text is non-empty:** I approve the action *with an adaptation*. Take what you were about to do and apply the change I described, then proceed. Show me the adjusted version only if the change is substantial enough that I'd want to see it before it lands; for a small, unambiguous tweak, just apply it and go.

If you were about to make several calls and only one was denied, retry only that one (with any adaptation) and then continue where you left off.
