# claude-wrap

**Version 1.2.1** | The `/wrap` end-of-session skill for Claude Code and Codex.

Run `/wrap` (Claude Code) or `$wrap` (Codex) when you finish a work session. The agent works
through, in order:

1. **Tests gate**: runs the project's test and typecheck commands. A failing tree gets fixed, or the agent stops and asks you how to proceed. A red tree is never committed.
2. **Diff review**: shows `git diff HEAD` for the session's changes.
3. **Changelog entry**: what changed and why, verification numbers, gotchas and decisions, and bundle size change.
4. **Project doc update**: status tables or active-phase notes in CLAUDE.md, AGENTS.md or the README, only where state changed.
5. **Resumption note**: `HANDOFF.md` with Verify first, Resume here, In flight, Deferred, Decisions and why, Tried and rejected, and Next session sections. CLAUDE.md imports it with `@HANDOFF.md`, and AGENTS.md gets a line telling Codex to read it, so the next session in either tool loads it.
6. **Memory sync**: in Claude Code, refreshes memory entries the session made stale. Codex has no memory store, so lasting rules go into AGENTS.md / CLAUDE.md instead.
7. **Commit**: a conventional-commit message, including the note, with the attribution trailer your setup specifies (none if it specifies none).
8. **Push**: `git push` to the remote. If a sandbox blocks the network, the agent gives you the command instead.
9. **Report**: the commit hash, where the note is, anything deferred, and how to start the next session.

Ask for a **handoff** instead (`/wrap handoff`, `$wrap handoff`) when you stop mid-task, for example
because context is getting large. It runs the tests and records the result without being blocked by
it, writes the note and syncs memory, and does not commit or push.

The note is plain markdown, so you can wrap in one tool and continue in the other.

## Install

### Claude Code

```
/plugin marketplace add kikostefanov-lab/claude-wrap
/plugin install wrap@claude-wrap
/reload-plugins
```

Then run `/wrap` (or `/wrap:wrap`).

### Codex

```
codex plugin marketplace add kikostefanov-lab/claude-wrap
```

```
codex plugin add wrap@claude-wrap
```

Or install the skill by hand, without the plugin system:

```
mkdir -p ~/.agents/skills && cp -R skills/wrap ~/.agents/skills/
```

Then run `$wrap`, or ask Codex to "wrap up the session".

Codex support is new in 1.2.0. Installing has been tested with codex-cli 0.167.0; running the skill
in a Codex session has not been tested yet.

## Repository layout

| Path | Used by |
|---|---|
| `skills/wrap/SKILL.md` | Both tools: the skill itself, the only copy of the instructions |
| `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json` | Claude Code plugin and marketplace |
| `plugin.json` | Codex portable plugin manifest |
| `.agents/plugins/marketplace.json` | Codex marketplace |

## Updating

### Publishing a new version

Edit `skills/wrap/SKILL.md`. Bump the version in `plugin.json`, `.claude-plugin/plugin.json`, both
`version` fields of `.claude-plugin/marketplace.json`, and the **Version** line above, add a line
to the history below, commit and push.

### Getting the new version

Updating a marketplace only downloads the latest list of plugins. It does **not** upgrade the copy
you have installed, so each tool needs two steps.

**Claude Code**, from a shell (the `/plugin` menu does not work in the desktop app):

```
claude plugin marketplace update claude-wrap
```

```
claude plugin update wrap@claude-wrap
```

Then start a new session or run `/reload-plugins`. Check with `claude plugin list`, which shows the
installed version.

In the terminal CLI you can use `/plugin marketplace update claude-wrap`, and then update `wrap`
from the `/plugin` menu.

**Codex:**

```
codex plugin marketplace upgrade claude-wrap
```

```
codex plugin add wrap@claude-wrap
```

## Version history

| Version | Changes |
|---|---|
| 1.2.1 | Fixes the mode check. Claude Code replaces the arguments placeholder everywhere it appears in the skill, so the old "does it still read as the placeholder?" test was compared against its own substituted value. The placeholder now appears once, and the check is "empty, or still a `$` placeholder". |
| 1.2.0 | Works in Codex as well as Claude Code. The command is now a shared skill (`skills/wrap/SKILL.md`) with Codex manifests (`plugin.json`, `.agents/plugins/marketplace.json`) next to the Claude Code ones. The note is pointed to from AGENTS.md as well as imported by CLAUDE.md. Memory sync, the attribution trailer and push now each cover the tool that lacks the feature: Codex has no memory store, the trailer may be unset, and Codex's sandbox can block the network. |
| 1.1.0 | Adds `/wrap handoff` for mid-task breaks (no tests block, no commit or push). The resumption note now has a fixed home (`HANDOFF.md`, imported from CLAUDE.md, unless the project already keeps a "next session" section) and a fixed structure: Verify first, Deferred, Decisions and why, and Tried and rejected. The note and memory sync now run before the commit, so the note is committed and pushed. Unfixable test failures stop the wrap and go to Deferred instead of being dropped. |
| 1.0.0 | First release as a plugin. Moved from a local `~/.claude/commands/wrap.md`; the commit step now uses the attribution trailer your setup specifies instead of a hard-coded model name. |

## License

MIT
