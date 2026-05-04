# Basic Memory Plugins

**Claude Code-specific plugins** for [Basic Memory](https://basicmemory.com) knowledge management.

This repository uses [Claude Code's plugin format](https://docs.claude.com/en/docs/claude-code/plugins) — slash commands, hooks, and skills bundled into an installable marketplace. It only works with Claude Code.

> **Looking for framework-agnostic skills?** See [`basic-memory-skills`](https://github.com/basicmachines-co/basic-memory-skills) — `SKILL.md` files that work in Claude Code, Claude Desktop, and other MCP-compatible agents. Some functionality (commands, hooks) is unique to this Claude Code plugin and isn't available there.

## Installation

Add the marketplace and install the plugin:

```bash
/plugin marketplace add basicmachines-co/basic-memory-plugins
/plugin install basic-memory@basicmachines-co
```

## Available Plugins

### basic-memory

Skills, commands, and hooks for [Basic Memory](https://github.com/basicmachines-co/basic-memory) MCP server integration.

**Skills:**
- `placement` - Decide which folder a new note belongs in (runs automatically before `write_note`)
- `knowledge-capture` - Capture important information from conversations
- `continue-conversation` - Continue previous conversations with context
- `knowledge-organize` - Maintain and organize the knowledge graph
- `research` - Research topics using web search and save to memory
- `edit-note` - Edit existing notes in the knowledge base

**Commands:**
- `/remember` - Capture knowledge from the current conversation
- `/context` - Build context from memory URLs
- `/organize` - Maintain knowledge graph structure
- `/research` - Research a topic and save findings

**Hooks:**
- Pre-write placement (selects the right folder based on project conventions)
- Post-write confirmation

## Configuration

The plugin reads conventions from a unified config file:

- **Per-project:** a note titled `basic-memory` at the project root
- **Global:** `~/.basic-memory/basic-memory.md`

Sections (`## Projects`, `## Placements`, `## Formats`, `## Schemas`) define rules. H3 sub-sections (e.g. `### research`) provide project-specific overrides.

If no config exists, the plugin uses sensible built-in defaults. See [PLUGIN.md](./PLUGIN.md) for the full schema and a bootstrap walkthrough.

## Requirements

- [Basic Memory](https://github.com/basicmachines-co/basic-memory) MCP server must be configured
- Claude Code CLI

## Documentation

See [PLUGIN.md](./PLUGIN.md) for full documentation.

## License

MIT
