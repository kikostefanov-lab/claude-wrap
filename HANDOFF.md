# Handoff — 2026-11-24 — main — full wrap — written by Claude Code

## Verify first
This note is a hint, not a fact. Before acting on it, check it against fresh output:
- Base commit: d43e52d. Run `git log --oneline d43e52d..HEAD` — you should see only the wrap commit;
  anything more happened after this note.
- Run `git status`. Uncommitted when written: none.
- Tests when written: pass (`claude plugin validate .`, all manifests valid JSON, versions all 1.2.0).
  Pushed: yes.
- Any path, function or command named below: confirm it exists before relying on it.

## Resume here
`skills/wrap/SKILL.md`, "Pick the mode": fix the `$ARGUMENTS` fallback sentence (see Deferred), then
release 1.2.1 following the README's "Publishing a new version" checklist.

## In flight
Nothing half-done. Open: running `$wrap` in a live Codex session is still untested.

## Deferred
- `$ARGUMENTS` fallback is wrong in Claude Code — it substitutes *both* occurrences, so the check
  "if that still reads literally as `$ARGUMENTS`" becomes "if that still reads literally as ``" (seen
  in this wrap). Harmless today, confusing always. Fix: say "if the line above is empty or shows the
  placeholder name, take the mode from the request" without repeating the placeholder.
- Live Codex test of `$wrap` and `$wrap handoff` — needs a signed-in Codex session.
- Codex upgrade path (`codex plugin marketplace upgrade` + `codex plugin add` replacing 1.2.0) —
  needs a newer release to test against; 1.2.1 will do.
- Plugin icon (validator warning ICON_MISSING) — optional, 512–2048 px square PNG.

## Decisions and why
- One `skills/wrap/SKILL.md` for both tools — two copies would drift. Settled.
- Handoff mode does not commit or push — no red commits on main; the work stays on disk. Settled.
- Note and memory sync run before the commit — 1.0.0 wrote the note after the push, so it never
  shipped. Settled.
- Codex marketplace entry uses `"source": {"source": "local", "path": "./"}` — documented keys, and
  tested with codex-cli 0.167.0. The `"url"` form has no documented shape. Settled.
- Root `plugin.json` (portable) rather than `.codex-plugin/` — OpenAI's docs make root primary.
- Releases go straight to `main` — the user's choice for this repo.

## Tried and rejected
- Reading the Reddit thread via Claude in Chrome or WebFetch — both block reddit.com; the user
  supplied a PDF export instead.
- `/plugin marketplace update` in the desktop app — the `/plugin` dialog doesn't work there. Use
  `claude plugin marketplace update claude-wrap` then `claude plugin update wrap@claude-wrap`.

## Next session
Claude Code, any current model at medium effort: a one-sentence skill fix, a patch release, and a
manual Codex test.
