---
description: End-of-session wrap — commit, push, update changelog and memory, write resumption note
user-invocable: true
---

# /wrap — Session Wrap

Run this at the end of every work session. Steps in order:

1. **Tests gate** — run the project's test and typecheck commands. If either fails, fix before proceeding. Do not wrap a broken tree.

2. **Show the diff** — `git diff HEAD` to review what changed this session.

3. **Changelog entry** — append a full per-commit narrative to the project's changelog:
   - What changed and why
   - Verification numbers (tests, smoke items)
   - Any gotchas or decisions made
   - Bundle delta if applicable

4. **Project doc update** — update any status tables or active-phase notes in the project's main context file (CLAUDE.md, README, etc.) only if something actually changed state. One line max per item.

5. **Commit** — stage relevant files and commit with a conventional-commit message:
   ```
   type(scope): short description

   - bullet points of what changed
   - any decisions or risks introduced

   <attribution trailer, if your setup specifies one>
   ```
   End the message with the commit attribution line your Claude Code setup specifies (for example a `Co-Authored-By:` trailer naming the current model). Never hard-code a model name.

6. **Push** — `git push` to the remote.

7. **Resumption note** — confirm the project's "next session" section is current:
   - Where exactly to resume (file, task, sub-commit)
   - Any in-flight state (open decisions, partial work)
   - Model recommendation for next session

8. **Memory sync** — update any stale memory entries that this session changed.
