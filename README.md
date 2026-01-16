# fix-my-claude

A Claude Code tool that audits and fixes repositories with bloated CLAUDE.md files or messy Claude configurations.

## The Problem

CLAUDE.md files tend to grow unbounded. Research shows LLMs reliably follow ~150-200 instructions max. Beyond that, earlier instructions get deprioritized ("context drift"), Claude contradicts rules, and performance degrades.

Common issues this tool fixes:
- CLAUDE.md files over 300 lines
- Code snippets that go stale
- Style guides that belong in linter configs
- Missing build/test commands
- Everything crammed into one file

## Installation

Clone this repository and use the commands from within Claude Code.

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
| `/project:audit <path>` | Read-only health check |
| `/project:fix <path> [--simple\|--complex]` | Main fixer |
| `/project:migrate <path> <--to-simple\|--to-complex>` | Convert setup type |

## Simple vs Complex

### Simple Setup (Default)
Best for most projects. Creates:
- 50-150 line CLAUDE.md
- 2-3 essential commands
- Basic hooks for formatting

**Choose simple when:**
- Project has < 50 files
- Solo developer or small team
- Team is new to Claude Code
- Straightforward tech stack

### Complex Setup
For large codebases with heavy Claude usage. Creates:
- 30-80 line orchestration-focused CLAUDE.md
- Multiple specialized subagents
- Rich command library
- MCP server integrations

**Choose complex when:**
- Large codebase (100+ files)
- Monorepo structure
- Team heavily uses Claude Code daily
- Need specialized workflows (security review, debugging, etc.)

## Core Principles

### The 150-200 Rule
Good CLAUDE.md files have ~150-200 instructions max. Each bullet point or rule counts as ~1 instruction.

### WHAT/WHY/HOW Framework
Every CLAUDE.md should answer:
- **WHAT**: Tech stack, structure, key files
- **WHY**: Purpose, architectural decisions
- **HOW**: Build, test, lint commands

### What Goes Where

| Content | Location |
|---------|----------|
| Project rules | CLAUDE.md |
| Style rules | Linter configs (.eslintrc, .prettierrc, etc.) |
| Multi-step workflows | .claude/commands/ |
| Specialized personas | .claude/agents/ |
| Auto-formatting | hooks in .claude/settings.json |

## Red Flags This Tool Detects

1. **CLAUDE.md > 300 lines** - Too much to reliably follow
2. **Code snippets** - Go stale when code changes
3. **Style guides** - Should be in linter configs
4. **Missing commands** - Claude shouldn't guess your build system
5. **Duplicate information** - Wastes context, causes conflicts
6. **Vague instructions** - "Run tests" vs `bun test --coverage`

## Reference Documentation

- `.claude/docs/templates.md` - CLAUDE.md templates
- `.claude/docs/best-practices.md` - Detailed guidance
- `how-to-claude.md` - Full Claude Code setup guide

## License

MIT
# fix-my-claude
