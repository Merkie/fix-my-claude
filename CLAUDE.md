# fix-my-claude

Audits and fixes repos with bloated CLAUDE.md files or messy Claude Code configurations.

## Usage

```bash
# Quick health check (read-only)
/project:audit /path/to/repo

# Fix with simple setup (default - recommended for most projects)
/project:fix /path/to/repo

# Fix with full subagentic architecture
/project:fix /path/to/repo --complex

# Convert between setup types
/project:migrate /path/to/repo --to-simple
/project:migrate /path/to/repo --to-complex
```

## Commands

| Command | Purpose |
|---------|---------|
| `/project:fix <path> [--simple\|--complex]` | Main fixer |
| `/project:audit <path>` | Read-only health check |
| `/project:migrate <path> <--to-simple\|--to-complex>` | Convert setup type |

## Subagents

| Agent | Purpose | When to Use |
|-------|---------|-------------|
| `analyzer` | Analyzes existing configs (haiku) | Large CLAUDE.md files, complex existing setups |
| `writer` | Generates optimized configs (sonnet) | After analysis, when creating new files |

## Reference Docs

For detailed guidance, read these as needed:
- `.claude/docs/templates.md` - CLAUDE.md templates for simple/complex setups
- `.claude/docs/best-practices.md` - Core principles and red flags
- `how-to-claude.md` - Full Claude Code setup guide (for user reference)

## Core Rules

### The 150-200 Rule
Good CLAUDE.md files have ~150-200 instructions max. Beyond that = context drift.

### WHAT/WHY/HOW Framework
Every CLAUDE.md answers: What's the stack? Why does each part exist? How do I build/test/lint?

### What Goes Where

| Content | Location |
|---------|----------|
| Project rules | CLAUDE.md |
| Style rules | Linter configs |
| Workflows | .claude/commands/ |
| Isolated research | .claude/agents/ |

### Red Flags

1. CLAUDE.md > 300 lines
2. Code snippets (go stale)
3. Style guides (linter's job)
4. No build/test commands

## Simple vs Complex

**Simple** (default): 50-150 line CLAUDE.md, 2-3 commands, basic hooks

**Complex**: 30-80 line CLAUDE.md, multiple subagents, skills, MCP servers

## Do NOT

- Over-engineer simple projects
- Assume --complex without explicit flag
- Delete content without replacement
- Create docs unless asked
