# claude-wrap

**Version 1.1.0** | The `/wrap` end-of-session command for Claude Code.

Run `/wrap` when you finish a work session. Claude works through, in order:

1. **Tests gate**: runs the project's test and typecheck commands. A failing tree gets fixed, or Claude stops and asks you how to proceed. A red tree is never committed.
2. **Diff review**: shows `git diff HEAD` for the session's changes.
3. **Changelog entry**: what changed and why, verification numbers, gotchas and decisions, and bundle size change.
4. **Project doc update**: status tables or active-phase notes in CLAUDE.md or the README, only where state changed.
5. **Resumption note**: `HANDOFF.md` (imported from CLAUDE.md so the next session loads it) with Verify first, Resume here, In flight, Deferred, Decisions and why, Tried and rejected, and Next session sections.
6. **Memory sync**: refreshes memory entries the session made stale.
7. **Commit**: a conventional-commit message, including the note, with the attribution trailer your setup specifies.
8. **Push**: `git push` to the remote.
9. **Report**: the commit hash, where the note is, anything deferred, and how to start the next session.

Run `/wrap handoff` instead when you stop mid-task, for example because context is getting large.
It runs the tests and records the result without being blocked by it, writes the note and syncs
memory, and does not commit or push.

## Install

```
/plugin marketplace add kikostefanov-lab/claude-wrap
/plugin install wrap@claude-wrap
/reload-plugins
```

Then run `/wrap` (or `/wrap:wrap`).

## Updating

Edit `commands/wrap.md`, bump the version in `.claude-plugin/plugin.json`, both `version` fields of
`.claude-plugin/marketplace.json`, and the **Version** line above, add a line to the history below,
commit and push. Then `/plugin marketplace update claude-wrap` and `/reload-plugins`.

## Version history

| Version | Changes |
|---|---|
| 1.1.0 | Adds `/wrap handoff` for mid-task breaks (no tests block, no commit or push). The resumption note now has a fixed home (`HANDOFF.md`, imported from CLAUDE.md, unless the project already keeps a "next session" section) and a fixed structure: Verify first, Deferred, Decisions and why, and Tried and rejected. The note and memory sync now run before the commit, so the note is committed and pushed. Unfixable test failures stop the wrap and go to Deferred instead of being dropped. |
| 1.0.0 | First release as a plugin. Moved from a local `~/.claude/commands/wrap.md`; the commit step now uses the attribution trailer your setup specifies instead of a hard-coded model name. |

## License

MIT
