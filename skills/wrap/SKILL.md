---
name: wrap
description: End-of-session wrap — tests gate, changelog, resumption note, memory sync, commit, push. Use when the user asks to wrap up, end, or hand off a session. "wrap handoff" is a mid-task context break that does not commit or push.
argument-hint: "[handoff]"
---

# Wrap — Session Wrap

This skill runs in Claude Code (`/wrap`) and Codex (`$wrap`). Where a step depends on the tool, it
says so.

## Pick the mode

Arguments, if your tool passes them:

> $ARGUMENTS

If the quoted line above is empty, or shows a `$`-prefixed placeholder name instead of real words,
your tool passed no arguments: take the mode from the user's request instead.

- **Full wrap** (no arguments, or "wrap up"): the work is at a stopping point. Gate on tests,
  record, commit, push.
- **Handoff** (`handoff`, or "hand off", "context is getting full", "continue in a new session"):
  the session is ending mid-task. The tree may be red. Write the note so a fresh session can
  continue; do not commit or push.

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
     whether to keep working on it or switch to a handoff. Never commit a red tree, and never drop
     the failure silently: in a handoff it goes in the note under **Deferred**.
   - Handoff: run them, record pass/fail and the failing test names in the note. Do not fix.

2. **Show the diff** — `git diff HEAD` to review what changed this session.

3. **Changelog entry** — append a full per-commit narrative to the project's changelog:
   - What changed and why
   - Verification numbers (tests, smoke items)
   - Any gotchas or decisions made
   - Bundle delta if applicable

4. **Project doc update** — update any status tables or active-phase notes in the project's main
   context files (CLAUDE.md, AGENTS.md, README, etc.) only if something actually changed state.
   One line max per item.

5. **Resumption note** — write it where the next session, in either tool, will load it without being
   told to:
   - If the project already keeps a "next session" section, update it there.
   - Otherwise write `HANDOFF.md` at the repo root, then make sure each context file that exists
     points at it, adding the line once if it is missing:
     - `CLAUDE.md` (Claude Code): the import line `@HANDOFF.md`, which loads the note into every session.
     - `AGENTS.md` (Codex and other agents): the line
       `At session start, read HANDOFF.md and run its "Verify first" checks before acting on it.`
       `AGENTS.md` has no import syntax, so this instruction is what gets the note read.
   - If neither file exists, create the one for the tool you are running in, with just that line.

   **Replace** the note each time; do not append. History belongs in the changelog and git log.
   Keep it under about 40 lines: link to the changelog, specs or issues rather than copying them.
   Leave out a section only if it is truly empty. Use this structure:

   ```markdown
   # Handoff — <YYYY-MM-DD> — <branch> — <full wrap | handoff> — written by <Claude Code | Codex>

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
   <tool, model and effort recommendation, and why>
   ```

   **Deferred** is mandatory whenever anything the session started, or was asked to do, is not done.
   Nothing is dropped silently.

6. **Memory sync** — depends on the tool:
   - If your tool keeps its own persistent memory (Claude Code's memory directory), update any
     entries this session made stale.
   - If it does not (Codex), there is nothing to sync. Lasting rules that should bind every future
     session go in `AGENTS.md` / `CLAUDE.md` instead, one line each; everything else stays in the note.

7. **Commit** — stage relevant files (including the note and any context file you touched) and
   commit with a conventional-commit message:
   ```
   type(scope): short description

   - bullet points of what changed
   - any decisions or risks introduced

   <attribution trailer, if your setup specifies one>
   ```
   End the message with the commit attribution line your agent setup specifies (for example a
   `Co-Authored-By:` trailer naming the current model). If it specifies none, add none. Never
   hard-code a model name.

8. **Push** — `git push` to the remote. If the push fails because the sandbox blocks network access
   (common in Codex's default sandbox), do not work around it: report it and give the user the exact
   command to run.

9. **Report** — tell the user, in a few lines: the mode, the commit hash and whether it was pushed
   (or "not committed" for a handoff), where the note is, anything under **Deferred**, and how to
   start the next session. If `CLAUDE.md` imports the note or `AGENTS.md` points at it, a fresh
   session only needs "continue from the handoff"; otherwise give the user the line to paste:
   "Read HANDOFF.md, run its Verify-first checks, then continue from Resume here."
