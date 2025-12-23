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
- `knowledge-capture` - Capture important information from conversations
- `continue-conversation` - Continue previous conversations with context
- `spec-driven-development` - Follow specification-driven development workflow
- `knowledge-organize` - Maintain and organize the knowledge graph
- `research` - Research topics using web search and save to memory
- `edit-note` - Edit existing notes in the knowledge base
- `validate-memo` - Validate memo formatting before saving

**Commands:**
- `/remember` - Capture knowledge from the current conversation
- `/continue` - Continue a previous conversation topic
- `/context` - Build context from memory URLs
- `/recent` - View recent activity in memory
- `/organize` - Maintain knowledge graph structure
- `/research` - Research a topic and save findings

**Hooks:**
- Pre-write validation (with [basic-memory-hooks](https://github.com/basicmachines-co/basic-memory-hooks))
- Post-write confirmation
- End-of-conversation `/remember` suggestion

## Optional: Memo Validation

For consistent, machine-readable memos, add [basic-memory-hooks](https://github.com/basicmachines-co/basic-memory-hooks):

```bash
# Clone and install
gh repo clone basicmachines-co/basic-memory-hooks
cd basic-memory-hooks
uv sync

# Start validation server
uv run python -m basic_memory_hooks

# Verify
curl http://localhost:8000/health
```

The plugin will automatically validate memos when the hooks server is running.

## Requirements

- [Basic Memory](https://github.com/basicmachines-co/basic-memory) MCP server must be configured
- Claude Code CLI
- (Optional) [basic-memory-hooks](https://github.com/basicmachines-co/basic-memory-hooks) for validation

## Documentation

See [PLUGIN.md](./PLUGIN.md) for full documentation.

## License

MIT
