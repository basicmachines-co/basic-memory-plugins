---
description: Capture insights, decisions, or learnings to Basic Memory
argument-hint: [title] [optional: folder]
allowed-tools: mcp__basic-memory__write_note, mcp__basic-memory__search_notes, WebFetch
---

# Remember

Capture what we just discussed into a Basic Memory note.

## Arguments

- `$1` - Title for the note (required)
- `$2` - Folder to save in (optional, defaults to "notes")

## Your Task

Create a structured note capturing the key insights from our conversation.

1. **Analyze the conversation** for:
   - Decisions made
   - Insights discovered
   - Problems solved
   - Patterns identified
   - Trade-offs discussed

2. **Structure the note** with:
   - Clear title: "$1" (or generate one if not provided)
   - Context section explaining the situation
   - Main content with key points
   - Observations using `[category]` format:
     - `[decision]` - Choices made
     - `[insight]` - Understanding gained
     - `[pattern]` - Reusable approaches
     - `[learning]` - Lessons learned
   - Relations to link related concepts with `[[WikiLinks]]`

3. **Save using** `mcp__basic-memory__write_note`:
   - The placement skill will determine the correct `folder` automatically (via the PreToolUse hook). If "$2" is provided, use it as an override.
   - Include relevant tags
   - Project: use the project specified by the user, or fall back to the default per `## Projects` rules in `~/.basic-memory/basic-memory.md`

4. **Confirm** what was captured and where it was saved.
