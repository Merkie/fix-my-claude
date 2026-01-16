# CLAUDE.md Templates

Reference templates to use when generating new CLAUDE.md files.

## Simple Setup Template (50-150 lines)

```markdown
# [Project Name]

[One sentence description - what this project does]

## Stack

- Language: [e.g., TypeScript, Python, Go]
- Framework: [e.g., Next.js, FastAPI, none]
- Package manager: [e.g., bun, npm, poetry]
- Database: [if applicable]

## Structure

- `src/` - [Main source code]
- `tests/` - [Test files]
- `[other]/` - [Purpose]

## Commands

```bash
# Install dependencies
[command]

# Run development server
[command]

# Build for production
[command]

# Run tests
[command]

# Lint and format
[command]

# Type check
[command]
```

## Key Files

- `src/[important].ts` - [What it does]
- `src/[config].ts` - [Configuration for what]

## Rules

- [Rule 1 - something linters can't enforce]
- [Rule 2 - architectural decision]
- [Rule 3 - project convention]
```

---

## Complex Setup Template (30-80 lines)

```markdown
# [Project Name]

[One sentence description]

## Architecture

[2-3 sentences on high-level architecture]

Key areas:
- `src/core/` - [Core business logic]
- `src/api/` - [API layer]
- `src/[domain]/` - [Domain-specific code]

## Commands

```bash
[command]   # Build
[command]   # Test
[command]   # Lint
```

## Subagents

Use these for specialized tasks:
- `code-reviewer` - PR reviews, code quality analysis
- `test-writer` - Generate test cases
- `debugger` - Investigate issues, trace bugs

## Slash Commands

- `/project:debug` - Debug workflow
- `/project:review` - Code review workflow
- `/project:[other]` - [Purpose]

## Critical Rules

- [Only rules that affect architecture]
- [Security-critical conventions]
```

---

## Subagent Template

```markdown
---
name: [kebab-case-name]
description: [One line - Claude matches tasks to this]
tools:
  - Read
  - Grep
  - Glob
  - [others as needed]
model: [haiku|sonnet|opus - haiku for simple, sonnet for complex]
---

# [Agent Name]

[What this agent does]

## When to Use

- [Scenario 1]
- [Scenario 2]

## Process

1. [Step 1]
2. [Step 2]
3. [Step 3]

## Output Format

[Expected output structure]

## Rules

- [Agent-specific rules]
```

---

## Command Template

```markdown
# [Command Name]

[What this command does]

Target: $ARGUMENTS

## Steps

1. [First step]
2. [Second step]
3. [Third step]

## Output

[What to show the user]

## Error Handling

- If [condition]: [action]
```

---

## settings.json Template

```json
{
  "permissions": {
    "allow": [
      "Read",
      "Write",
      "Edit",
      "Glob",
      "Grep",
      "Bash([package-manager]:*)",
      "Bash(git:*)"
    ],
    "deny": [
      "Bash(rm -rf:*)",
      "Bash(sudo:*)"
    ]
  },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "[formatter] $CLAUDE_FILE_PATHS"
          }
        ]
      }
    ]
  }
}
```

---

## .mcp.json Template

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```
