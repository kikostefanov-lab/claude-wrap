# claude-wrap

**Version 1.0.0** | The `/wrap` end-of-session command for Claude Code.

Run `/wrap` when you finish a work session. Claude works through, in order:

1. **Tests gate** — runs the project's test and typecheck commands; a failing tree is fixed before wrapping.
2. **Diff review** — `git diff HEAD` of the session's changes.
3. **Changelog entry** — what changed and why, verification numbers, gotchas and decisions, bundle delta.
4. **Project doc update** — status tables or active-phase notes in CLAUDE.md / README, only where state changed.
5. **Commit** — conventional-commit message with the attribution trailer your setup specifies.
6. **Push** — `git push` to the remote.
7. **Resumption note** — where to resume, in-flight state, model recommendation for the next session.
8. **Memory sync** — refresh memory entries the session made stale.

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
| 1.0.0 | First release as a plugin. Moved from a local `~/.claude/commands/wrap.md`; the commit step now uses the attribution trailer your setup specifies instead of a hard-coded model name. |

## License

MIT
