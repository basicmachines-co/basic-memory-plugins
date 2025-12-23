---
name: validate-memo
description: Validate and fix memo formatting using basic-memory-hooks before saving to Basic Memory
---

# Validate Memo

This skill validates memos against your project's format configuration before saving, catching LLM inconsistencies and fixing them automatically.

## When to Use

Use this skill when:
- Creating memos that should conform to project standards
- Using `/remember` or `/research` commands
- Writing structured notes with observations and relations
- You want consistent, machine-readable knowledge capture

## Prerequisites

### Install basic-memory-hooks

```bash
# Clone the repo
cd ~/code
gh repo clone basicmachines-co/basic-memory-hooks

# Install dependencies
cd basic-memory-hooks
uv sync  # or: pip install -e .
```

### Start the Validation Server

```bash
cd ~/code/basic-memory-hooks
uv run python -m basic_memory_hooks
```

The server runs on `http://localhost:8000` by default.

### Verify It's Running

```bash
curl http://localhost:8000/health
# Returns: {"status":"healthy"}
```

## Validation Workflow

### 1. Check Current Configuration

```bash
curl http://localhost:8000/config
```

This shows:
- **Allowed note types**: memo, spec, decision, meeting, etc.
- **Required observation categories**: fact, decision, technique
- **Optional categories**: insight, question, idea, requirement, problem, solution
- **Quality requirements**: minimum observations, relations, tags

### 2. Validate Before Saving

```bash
curl -X POST http://localhost:8000/validate \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Your Memo Title",
    "content": "---\ntitle: Your Memo Title\ntype: memo\ntags:\n  - tag1\n---\n\n# Your Memo Title\n\n## Observations\n\n- [fact] Something factual\n- [decision] A choice made\n- [technique] An approach used\n\n## Relations\n\n- [[Related Note]]"
  }'
```

### 3. Review Results

The response includes:
- `success`: true/false based on strictness level
- `content`: Auto-fixed content (if fixable)
- `errors`: Issues that failed validation
- `warnings`: Issues that were auto-fixed or noted
- `metadata.hooks_run`: Which validation hooks ran

### 4. Save the Validated Content

Use the `content` from the response to save to Basic Memory.

## Common Validation Issues

### Missing Frontmatter Title

```yaml
# Wrong - title only in H1
---
type: memo
---
# My Title

# Right - title in frontmatter AND H1
---
title: My Title
type: memo
---
# My Title
```

### Missing Observations Section

```markdown
# Wrong - observations scattered in prose
Some facts here. A decision there.

# Right - consolidated Observations section
## Observations
- [fact] Some facts here
- [decision] A decision there
```

### Invalid Observation Categories

Default allowed categories:
- **Required**: `fact`, `decision`, `technique`
- **Optional**: `insight`, `question`, `idea`, `requirement`, `problem`, `solution`

```markdown
# Wrong - custom categories not in config
- [milestone] Launched product
- [achievement] Completed goal

# Right - use allowed categories
- [fact] Launched product
- [fact] Completed goal
```

### Wrong Observation Format

```markdown
# Wrong
- fact: This is a fact
- This is something

# Right
- [fact] This is a fact
- [fact] This is something
```

## MCP Integration

When using Claude Code or other MCP-enabled tools, the validation can be integrated into the write flow:

```python
# 1. Prepare content
content = prepare_memo_content(...)

# 2. Validate via API
response = requests.post(
    "http://localhost:8000/validate",
    json={"title": title, "content": content}
)
result = response.json()

# 3. Check result
if result["success"]:
    validated_content = result["content"]
    # Save to Basic Memory
    mcp__basic-memory__write_note(
        title=title,
        content=validated_content,
        folder="memos",
        project="main"
    )
else:
    # Handle errors
    print("Validation errors:", result["errors"])
```

## Strictness Levels

Configure in `.basic-memory/format.md`:

- **strict**: Errors fail validation. For production pipelines.
- **balanced** (default): Errors become warnings. Fixes what it can.
- **flexible**: Only formatting hooks run. Maximum permissiveness.

## Custom Configuration

Create `.basic-memory/format.md` in your project:

```yaml
---
version: "1.0"
strictness: balanced

note_types:
  allowed: [memo, spec, decision, project]

observation_categories:
  required: [fact]
  optional: [decision, insight, technique, milestone, achievement]

quality:
  minimum_observations: 1
  require_tags: true
---

# My Project Format

Custom format rules for this project.
```

## Best Practices

1. **Start the server before writing memos** - Run it in background
2. **Check config first** - Know what categories are allowed
3. **Use the validated content** - The API auto-fixes many issues
4. **Iterate on warnings** - Even passing validations may have warnings
5. **Customize for your project** - Add categories that match your workflow

## Quick Reference

| Endpoint | Purpose |
|----------|---------|
| `GET /health` | Check server is running |
| `GET /config` | View current validation rules |
| `POST /validate` | Validate memo content |
| `GET /docs` | Swagger UI documentation |
