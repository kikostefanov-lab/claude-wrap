# Handoff — 2026-11-24 — main — full wrap — written by Claude Code

## Verify first
This note is a hint, not a fact. Before acting on it, check it against fresh output:
- Base commit: 2a0ff03 (the 1.2.1 release). Run `git log --oneline 2a0ff03..HEAD` — you should see
  only the commit that updated this note; anything more happened after it.
- Run `git status`. Uncommitted when written: none.
- Tests when written: pass (`claude plugin validate .`; manifests valid JSON; versions all 1.2.1).
  Pushed: yes. Installed: 1.2.1 in Claude Code and in Codex.
- Any path, function or command named below: confirm it exists before relying on it.

## Resume here
Open a Codex session in any repo with uncommitted work and run `$wrap handoff`. Check that it picks
handoff mode, writes `HANDOFF.md`, adds the read-HANDOFF line to `AGENTS.md`, and does not commit.

## In flight
Nothing half-done.

## Deferred
- Live Codex run of `$wrap` / `$wrap handoff` — needs a signed-in Codex session; see Resume here.
- Plugin icon (validator ICON_MISSING) — optional, 512–2048 px square PNG.

## Decisions and why
- One `skills/wrap/SKILL.md` for both tools — two copies would drift. Settled.
- `$ARGUMENTS` appears exactly once in the skill — Claude Code substitutes every occurrence, so a
  second copy can't be used to detect substitution (fixed in 1.2.1). Settled.
- Handoff mode does not commit or push — no red commits on main; work stays on disk. Settled.
- Note and memory sync run before the commit — 1.0.0 wrote the note after the push. Settled.
- Codex marketplace entry uses `{"source": "local", "path": "./"}` — documented keys; install and
  upgrade both tested with codex-cli 0.167.0. Settled.
- `CLAUDE.md` at the repo root is for working on this repo; the validator warns it isn't loaded for
  plugin users, which is intended. Ignore that warning.
- Releases go straight to `main` — the user's choice for this repo.

## Tried and rejected
- Reading Reddit via Claude in Chrome or WebFetch — both block reddit.com; user supplied a PDF.
- `/plugin marketplace update` in the desktop app — doesn't work there; use the shell commands in
  the README's "Getting the new version".
- `codex plugin list` against the user's real Codex config — hung for over 2 minutes; run
  `codex plugin marketplace upgrade claude-wrap` and `codex plugin add wrap@claude-wrap` directly,
  then check `~/.codex/plugins/cache/claude-wrap/wrap/` for the version. macOS has no `timeout`.

## Next session
Codex for the live test, then Claude Code at medium effort for any fix it turns up.
