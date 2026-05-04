# Basic Memory Plugin for Claude Code

This plugin provides skills, commands, and hooks for working with [Basic Memory](https://basicmemory.com) — a local-first knowledge management system built on the Model Context Protocol (MCP).

It uses [Claude Code's plugin format](https://docs.claude.com/en/docs/claude-code/plugins) (slash commands, hooks, and skills bundled into an installable marketplace) and **only works with Claude Code**.

> **Looking for framework-agnostic skills?** See [`basic-memory-skills`](https://github.com/basicmachines-co/basic-memory-skills) — `SKILL.md` files that work in Claude Code, Claude Desktop, and other MCP-compatible agents. The commands and hooks in this plugin are Claude Code-specific and aren't available there.

## Prerequisites

You need the Basic Memory MCP server running. Install it via:

```bash
# Install basic-memory
pip install basic-memory

# Or with pipx
pipx install basic-memory
```

Then add it to your Claude Code MCP configuration.

## Installation

### Add the Marketplace

```
/plugin marketplace add basicmachines-co/basic-memory-plugins
```

### Install the Plugin

```
/plugin install basic-memory@basicmachines
```

### Or via Repository Settings

Add to your `.claude/settings.json`:

```json
{
  "plugins": {
    "extraKnownMarketplaces": {
      "basicmachines": {
        "source": {
          "source": "github",
          "repo": "basicmachines-co/basic-memory-plugins"
        }
      }
    },
    "installed": ["basic-memory@basicmachines"]
  }
}
```

---

## Configuration

The plugin reads project conventions from a unified config file. Without one, it falls back to sensible built-in defaults. You can adopt the config gradually as you discover what you want to standardize.

### Where it lives

| Scope | Location | Format |
|-------|----------|--------|
| Project | A note titled `basic-memory` at the project root | Basic Memory note (read via MCP) |
| Global | `~/.basic-memory/basic-memory.md` | Filesystem markdown file |

### Schema

The config file uses H2 sections for categories and H3 sub-sections for project-specific overrides. Bare content under an H2 is the default; H3 sub-sections override it for a specific project.

```markdown
# Basic Memory config

## Projects
- work: default project for daily work
- personal: personal notes and reflections
- research: long-form research notes

## Placements
- Place into existing folders by topic match
- Never create new top-level folders without asking
- Match the project's existing naming convention

### research
- Long-form notes go in `papers/`
- Quick references go in `refs/`

## Formats
- Required frontmatter: title, type, date
- Observation categories: fact, decision, technique, problem, solution

## Schemas
### work
person:
  - name
  - email
  - role
```

### Reserved sections

| Section | Scope | Purpose |
|---------|-------|---------|
| `## Projects` | Global only | Routing rules — when to use which project |
| `## Placements` | Project or global | Folder conventions for new notes |
| `## Formats` | Project or global | Frontmatter and observation conventions |
| `## Schemas` | Project or global | Note type definitions |

Other H2 sections are treated as user notes and ignored.

### Precedence

For each section the plugin needs:

1. Project's `basic-memory` note → if the section exists, use it
2. Global `~/.basic-memory/basic-memory.md` → look for `### <project>` first, then bare content under the H2
3. Built-in defaults

Section-level fallback means a project file can override one section while inheriting others from global.

### Bootstrap

For an existing project with established conventions, ask Claude to generate a starter `basic-memory.md` based on what's already in the project:

> "Look at my `<project-name>` project structure and generate a starter `basic-memory` note for it. Inspect the folder layout and existing notes to infer placement and format conventions."

Claude will:
1. Inspect the project tree (`list_directory`) and sample notes from each folder
2. Infer naming conventions, depth patterns, and organizational structure
3. Draft a `basic-memory` note with `## Placements` populated and other sections as commented placeholders
4. Show you the draft for review before writing

This is a one-time conversational pattern — no slash command required.

---

## Slash Commands

User-invoked commands for explicit interaction with Basic Memory.

### `/remember [title] [folder]`

Capture insights, decisions, or learnings from the current conversation.

```
/remember "FastAPI Async Pattern"
/remember "Auth Decision" decisions
```

Creates a structured note with:
- Context from the conversation
- Observations with `[decision]`, `[insight]`, `[pattern]` categories
- Relations linking to related concepts

The `placement` skill chooses the folder automatically based on project conventions. Pass an explicit folder to override.

### `/continue [topic]`

Resume previous work by building context from Basic Memory.

```
/continue postgres migration
/continue SPEC-24
/continue
```

If no topic is provided, shows recent activity and asks what to dive into.

### `/context <memory://url> [depth] [timeframe]`

Build context from a specific memory:// URL.

```
/context memory://SPEC-24
/context memory://architecture/* 3 2weeks
```

### `/recent [timeframe] [project]`

Show recent activity in Basic Memory.

```
/recent
/recent 1week
/recent today specs
```

### `/organize [action] [project]`

Organize and maintain your knowledge graph.

```
/organize                    # Quick health check
/organize orphans            # Find unlinked notes
/organize duplicates         # Find similar notes
/organize relations "Note"   # Suggest links for a note
/organize tags               # Review tag consistency
```

Actions:
- `health` - Overview of knowledge base status (default)
- `orphans` - Find notes with no relations
- `duplicates` - Find overlapping notes
- `relations` - Suggest connections
- `tags` - Review tag consistency

### `/research <topic> [folder]`

Research a topic and save a structured report to Basic Memory.

```
/research MCP protocol
/research "database migrations"
/research "auth options" decisions
```

Produces a report with:
- Summary and key findings
- Analysis and recommendations
- Sources and related notes
- Saved to `research/` folder by default (or wherever the `placement` skill directs based on project conventions)

---

## Skills

Model-invoked capabilities that Claude uses automatically based on context.

### placement

Decides which folder a new note belongs in. Runs automatically before every `mcp__basic-memory__write_note` call (via PreToolUse hook).

**Triggers when:**
- About to call `mcp__basic-memory__write_note`
- Manually invoked when planning a write

**How it works:**
1. Reads project and global config (`basic-memory.md`) — extracts `## Placements` rules
2. Short-circuits at the first definitive answer:
   - Config rule applies → use it
   - Tree match obvious → use it
   - Search for related notes → use as a placement signal
3. Asks the user if placement remains ambiguous

**Best for:** Keeping notes organized according to project conventions without per-write instruction.

### knowledge-capture

Automatically captures insights, decisions, and learnings into structured notes.

**Triggers when:**
- Important decisions are made
- Technical insights are discovered
- Problems are solved
- Design trade-offs are discussed

### continue-conversation

Resumes previous work by building context from the knowledge graph.

**Triggers when:**
- Starting a new session
- User mentions previous work ("continue with...", "back to...")
- Need context about ongoing projects

### spec-driven-development

Guides implementation based on specifications stored in Basic Memory.

**Triggers when:**
- Implementing a feature defined by a spec
- Creating new specifications
- Reviewing implementation against criteria

### edit-note

Interactively edit notes using MCP tools in a conversational workflow.

**Triggers when:**
- User wants to edit, update, or modify a note
- User asks to change specific content in a note
- User wants to add observations or relations

**How it works:**
1. Fetches the note via MCP
2. Shows current content
3. Applies edits using `edit_note` operations (append, prepend, find_replace, replace_section)
4. Shows the updated result

### knowledge-organize

Help organize, link, and maintain the knowledge graph.

**Triggers when:**
- User wants to organize their notes
- User asks about orphan or unlinked notes
- User wants to find connections between notes
- User mentions duplicates or similar notes
- User asks for help with folder organization

**Capabilities:**
- **Find orphan notes** - Identify notes with no relations
- **Suggest relations** - Propose meaningful links between notes
- **Identify duplicates** - Find notes covering similar topics
- **Folder organization** - Review and suggest folder structure
- **Tag consistency** - Normalize and improve tagging
- **Create index notes** - Generate hub notes linking related topics
- **Enrich sparse notes** - Suggest observations and structure

**Best for:** Periodic knowledge base maintenance and improving discoverability.

### research

Research topics thoroughly and produce structured reports saved to Basic Memory.

**Triggers when:**
- User asks to research or investigate something
- User wants to understand a concept or technology
- User needs context before making a decision
- Phrases like "research", "look into", "explore", "investigate"

**What it produces:**
- Structured report with summary, findings, and analysis
- Recommendations when applicable
- Links to sources and related notes
- Saved to `research/` folder (or wherever `placement` directs)

**Best for:** Building knowledge base through investigation and documentation.

---

## Hooks

Automated behaviors that enhance the Basic Memory workflow.

### PreToolUse: write_note

Invokes the `placement` skill before saving a note, ensuring the `directory` parameter matches project conventions defined in `basic-memory.md`.

### PostToolUse: write_note

Confirms when notes are saved to Basic Memory.

### Stop

After significant conversations, suggests using `/remember` to capture valuable insights (only when genuinely useful).

---

## MCP Tools Used

This plugin leverages Basic Memory's MCP tools:

| Tool | Purpose |
|------|---------|
| `write_note` | Create/update markdown notes |
| `read_note` | Read notes by title or permalink |
| `search_notes` | Full-text search across content |
| `list_directory` | Inspect project folder structure |
| `build_context` | Navigate knowledge graph via memory:// URLs |
| `recent_activity` | Get recently updated information |
| `edit_note` | Incrementally update notes |

---

## Plugin Structure

```
basic-memory-plugins/
├── .claude-plugin/
│   ├── marketplace.json     # Marketplace manifest
│   └── plugin.json          # Plugin manifest
├── commands/
│   ├── remember.md          # /remember command
│   ├── continue.md          # /continue command
│   ├── context.md           # /context command
│   ├── recent.md            # /recent command
│   ├── organize.md          # /organize command
│   └── research.md          # /research command
├── skills/
│   ├── placement/           # NEW: folder placement
│   ├── knowledge-capture/
│   ├── continue-conversation/
│   ├── spec-driven-development/
│   ├── edit-note/
│   ├── knowledge-organize/
│   └── research/
├── hooks/
│   └── hooks.json           # Hook definitions
├── agents/
│   └── basic-memory-manager.md
├── README.md                # Quick start guide
└── PLUGIN.md                # Full documentation
```

---

## Related

- [Basic Memory Documentation](https://docs.basicmemory.com)
- [Basic Memory GitHub](https://github.com/basicmachines-co/basic-memory)
- [Model Context Protocol](https://modelcontextprotocol.io)
- [Claude Code Plugins](https://code.claude.com/docs/en/plugins)
