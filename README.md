# Basic Memory Plugins

Official Claude Code plugins from [Basic Machines](https://basicmachines.co) for knowledge management and AI-assisted development.

## Installation

Add the marketplace and install the plugin:

```bash
/plugin marketplace add basicmachines-co/basic-memory-plugins
/plugin install basic-memory@basicmachines
```

## Available Plugins

### basic-memory

Skills, commands, and hooks for [Basic Memory](https://github.com/basicmachines-co/basic-memory) MCP server integration.

**Skills:**
- `placement` - Decide which folder a new note belongs in (runs automatically before `write_note`)
- `knowledge-capture` - Capture important information from conversations
- `continue-conversation` - Continue previous conversations with context
- `spec-driven-development` - Follow specification-driven development workflow
- `knowledge-organize` - Maintain and organize the knowledge graph
- `research` - Research topics using web search and save to memory
- `edit-note` - Edit existing notes in the knowledge base

**Commands:**
- `/remember` - Capture knowledge from the current conversation
- `/continue` - Continue a previous conversation topic
- `/context` - Build context from memory URLs
- `/recent` - View recent activity in memory
- `/organize` - Maintain knowledge graph structure
- `/research` - Research a topic and save findings

**Hooks:**
- Pre-write placement (selects the right folder based on project conventions)
- Post-write confirmation
- End-of-conversation `/remember` suggestion

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
