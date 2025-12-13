# Basic Memory Plugins

Official Claude Code plugins from [Basic Machines](https://basicmachines.co) for knowledge management and AI-assisted development.

## Installation

Add the marketplace and install the plugin:

```bash
/plugin marketplace add github:basicmachines-co/basic-memory-plugins
/plugin install basic-memory@basicmachines
```

## Available Plugins

### basic-memory

Skills, commands, and hooks for [Basic Memory](https://github.com/basicmachines-co/basic-memory) MCP server integration.

**Skills:**
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
- Automatic context loading at conversation start

## Requirements

- [Basic Memory](https://github.com/basicmachines-co/basic-memory) MCP server must be configured
- Claude Code CLI

## License

MIT
