# Changelog

## 0.3.3

### Removed

- **Stop hook** that suggested `/remember` at conversation end. The hook entered an infinite re-entry loop in real-world testing: when the model finished its turn awaiting user input, the Stop hook fired with a "consider suggesting /remember" prompt; the model evaluated and decided no action was needed; the model tried to stop again; the hook fired again; and so on, with the user effectively unable to take their turn until interrupting manually.

### Why

The Stop hook predates 0.3.0 and was not modified by the placement work, but the v0.3.2 smoke test made the loop visible (since 0.3.2 was the first release whose hooks actually fired for the test environment — see 0.3.2 notes). Rather than ship a broken hook, removing it is the right call. Users can still invoke `/remember` manually.

A guarded re-entry-safe version may return in a later release once the Claude Code Stop-hook semantics (`stop_hook_active` and similar) are understood and can be wired in safely.

## 0.3.2

### Fixed

- **Hook matchers now use regex** — `mcp__.*__write_note` instead of the literal `mcp__basic-memory__write_note`. The previous matcher only fired for users running a locally-installed Basic Memory MCP server. Users on the claude.ai Basic Memory Cloud connector (or any other MCP server name) had hooks that silently never fired. The regex catches all variants.

### Changed

- Hook prompt body and skill/PLUGIN.md descriptions updated to be tool-name-agnostic, matching the new matcher.

### Known limitations

- Slash commands (`/remember`, `/research`, etc.) and the `basic-memory-manager` agent still have `allowed-tools` frontmatter that lists exact tool names (`mcp__basic-memory__*`). Users on alternative MCP server names may find these commands have no tool access. Pattern support in `allowed-tools` is being investigated for a follow-up release.

## 0.3.1

### Changed

- **Marketplace renamed** from `basicmachines` to `basicmachines-co` so the install identifier matches the GitHub org slug. Install command is now `/plugin install basic-memory@basicmachines-co` (was `basic-memory@basicmachines`).

### Migration

If you installed the plugin before 0.3.1:
1. Remove the old reference from `.claude/settings.json` (`basic-memory@basicmachines` in `enabledPlugins` or `installed`).
2. Re-install with the new identifier: `/plugin install basic-memory@basicmachines-co`.

The `extraKnownMarketplaces` block (if you used one) also needs the key updated from `"basicmachines"` to `"basicmachines-co"`.

## 0.3.0

### Added

- **`placement` skill** — decides which folder a new note belongs in. Runs automatically before every `mcp__basic-memory__write_note` call via a `PreToolUse` hook. Reads project conventions from a unified config file and applies a short-circuit decision flow (config → tree → search → ask).
- **Unified config file** (`basic-memory.md`) — a single config surface for project conventions. Lives as a Basic Memory note at the project root or as a global file at `~/.basic-memory/basic-memory.md`. Reserved sections: `## Projects`, `## Placements`, `## Formats`, `## Schemas`. H3 sub-sections provide project-specific overrides; bare H2 content is the default. Section-level fallback between project, global, and built-in defaults.
- **Bootstrap pattern** — documented conversational flow for generating a starter `basic-memory.md` from an existing project's structure. No new command; just ask Claude.

### Removed

- **`validate-memo` skill** and the entire integration with `basic-memory-hooks` (the external validation server). The model-driven approach replaces external validation.
- **`edit-note-local` skill** — its core dependency (`basic-memory sync --watch` running as a separate process) no longer exists; sync now runs automatically inside the MCP server. `edit-note` covers the remaining use cases.
- All references to `localhost:8000` / `localhost:4665`, `basic-memory-hooks`, and `.basic-memory/format.md` across docs and skills.

### Changed

- **`hooks/hooks.json`** — `PreToolUse` on `write_note` now invokes the `placement` skill instead of validate-memo.
- **`commands/remember.md`** — placement is automatic; `[folder]` argument is now an explicit override.
- **`PLUGIN.md`** — new "Configuration" section documenting the unified config schema, precedence, and bootstrap.
- **`README.md`** — removed hooks server quick-start; added pointer to the new configuration model.

### Migration

If you were using `basic-memory-hooks` for memo validation:
- Format rules previously kept in `.basic-memory/format.md` move to a `## Formats` section in `basic-memory.md`. They are now guidance for the model rather than externally enforced.
- The `basic-memory-hooks` server is no longer needed; you can remove it.

If you were using `edit-note-local`:
- Use `edit-note` instead. MCP `edit_note` operations (`append`, `prepend`, `find_replace`, `replace_section`) cover the same workflows.
