---
description: End-of-session wrap — commit, push, update changelog and memory, write resumption note. `/wrap handoff` for a mid-task context break.
argument-hint: "[handoff]"
user-invocable: true
---

# /wrap — Session Wrap

Arguments: `$ARGUMENTS`

## Pick the mode

- **Full wrap** (no arguments): the work is at a stopping point. Gate on tests, record, commit, push.
- **Handoff** (`/wrap handoff`): the session is ending mid-task, usually because context is getting
  large. The tree may be red. Write the note so a fresh session can continue; do not commit or push.

| Step | Full wrap | Handoff |
|---|---|---|
| 1. Tests gate | must pass, or stop (see step 1) | run and record the result; never blocks |
| 2. Show the diff | yes | yes |
| 3. Changelog entry | yes | skip |
| 4. Project doc update | yes | skip |
| 5. Resumption note | yes | yes |
| 6. Memory sync | yes | yes |
| 7. Commit | yes | skip, unless the user asks |
| 8. Push | yes | skip |
| 9. Report | yes | yes |

The note and the memory sync come **before** the commit so they are part of it and travel with the
branch. A note written after the push would sit uncommitted and never reach another machine.

## Steps

1. **Tests gate** — run the project's test and typecheck commands.
   - Full wrap: if either fails, fix it. If it cannot be fixed this session, **stop and ask** the user
     whether to keep working on it or switch to `/wrap handoff`. Never commit a red tree, and never
     drop the failure silently: in handoff mode it goes in the note under **Deferred**.
   - Handoff: run them, record pass/fail and the failing test names in the note. Do not fix.

2. **Show the diff** — `git diff HEAD` to review what changed this session.

3. **Changelog entry** — append a full per-commit narrative to the project's changelog:
   - What changed and why
   - Verification numbers (tests, smoke items)
   - Any gotchas or decisions made
   - Bundle delta if applicable

4. **Project doc update** — update any status tables or active-phase notes in the project's main context file (CLAUDE.md, README, etc.) only if something actually changed state. One line max per item.

5. **Resumption note** — write it where the next session will load it without being told to:
   - If the project already keeps a "next session" section (in CLAUDE.md or elsewhere), update it there.
   - Otherwise write `HANDOFF.md` at the repo root, and if CLAUDE.md does not already import it, add
     the line `@HANDOFF.md` to CLAUDE.md once so every new session loads it automatically.

   **Replace** the note each time; do not append. History belongs in the changelog and git log.
   Keep it under about 40 lines: link to the changelog, specs or issues rather than copying them.
   Leave out a section only if it is truly empty. Use this structure:

   ```markdown
   # Handoff — <YYYY-MM-DD> — <branch> — <full wrap | handoff>

   ## Verify first
   This note is a hint, not a fact. Before acting on it, check it against fresh output:
   - Base commit: <HEAD hash when this note was written>. Run `git log --oneline <base>..HEAD` —
     after a full wrap you should see only the wrap commit; anything more happened after this note.
   - Run `git status`. Uncommitted when written: <none | the files>.
   - Tests when written: <pass | fail: names>. Pushed: <yes | no>.
   - Any path, function or command named below: confirm it exists before relying on it.

   ## Resume here
   <file, task, and the exact next step — one action, not a list of options>

   ## In flight
   <half-done work and its state; open decisions waiting on the user>

   ## Deferred
   - <item> — <why it was not done> — <what unblocks it>

   ## Decisions and why
   - <decision> — <the reason>. Settled: do not reopen without new information.

   ## Tried and rejected
   - <approach> — <why it failed or was dropped>

   ## Next session
   <model and effort recommendation, and why>
   ```

   **Deferred** is mandatory whenever anything the session started, or was asked to do, is not done.
   Nothing is dropped silently.

6. **Memory sync** — update any stale memory entries that this session changed.

7. **Commit** — stage relevant files (including the note) and commit with a conventional-commit message:
   ```
   type(scope): short description

   - bullet points of what changed
   - any decisions or risks introduced

   <attribution trailer, if your setup specifies one>
   ```
   End the message with the commit attribution line your Claude Code setup specifies (for example a `Co-Authored-By:` trailer naming the current model). Never hard-code a model name.

8. **Push** — `git push` to the remote.

9. **Report** — tell the user, in a few lines: the mode, the commit hash and whether it was pushed
   (or "not committed" for a handoff), where the note is, anything under **Deferred**, and how to
   start the next session. If the note is imported by CLAUDE.md, a fresh session only needs
   "continue from the handoff"; otherwise give the user the line to paste: "Read HANDOFF.md, run its
   Verify-first checks, then continue from Resume here."
