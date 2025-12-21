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

3. **Validate the note** (optional, graceful degradation):
   - Try to POST the content to `http://localhost:8000/validate` using WebFetch
   - If the hooks API is available:
     - Use the returned `content` (which may have auto-fixes applied)
     - Note any `warnings` to mention to the user
   - If the API is unavailable (connection refused, timeout):
     - Continue with the original content - validation is optional
   - This step enhances quality but never blocks saving

4. **Save using** `mcp__basic-memory__write_note`:
   - folder: "$2" or "notes"
   - Include relevant tags
   - Project: use "main" unless user specifies otherwise

5. **Confirm** what was captured and where it was saved.
   - If validation ran, mention any warnings that were found
   - If validation was skipped, that's fine - don't mention it
