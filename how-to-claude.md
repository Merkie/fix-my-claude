# How to Set Up Your Project for Claude Code (2025 Edition)

A comprehensive guide to configuring Claude Code for maximum effectiveness. Stop fighting with long, bloated CLAUDE.md files and messy configurations.

---

## Table of Contents

1. [CLAUDE.md Best Practices](#claudemd-best-practices)
2. [Slash Commands](#slash-commands)
3. [Subagents](#subagents)
4. [Skills](#skills)
5. [Hooks](#hooks)
6. [MCP Servers](#mcp-servers)
7. [Settings & Permissions](#settings--permissions)
8. [Workflow Tips](#workflow-tips)
9. [When to Use What](#when-to-use-what)

---

## CLAUDE.md Best Practices

CLAUDE.md is a special file that Claude automatically loads into context. It's your primary way to give Claude project-specific knowledge.

### Location Options

| Location | Purpose |
|----------|---------|
| `./CLAUDE.md` | Project root (shared via git) |
| `./.claude/CLAUDE.md` | Alternative project location |
| `~/.claude/CLAUDE.md` | Global (applies to all projects) |
| `./subdir/CLAUDE.md` | Nested (auto-loaded when Claude reads from that directory) |

### The Golden Rule: Keep It SHORT

> "You're writing for Claude, not onboarding a junior dev."

Research suggests frontier LLMs can reliably follow ~150-200 instructions. Beyond that, expect "context drift" where earlier instructions get deprioritized.

### What to Include (WHAT/WHY/HOW Framework)

```markdown
# Project Name

## WHAT (Tech Stack & Structure)
- Framework: Next.js 14 with App Router
- Database: PostgreSQL with Prisma ORM
- Key directories: `src/app/` (routes), `src/lib/` (utilities)

## WHY (Purpose)
- E-commerce platform for artisan goods
- `src/lib/cart/` handles shopping cart logic
- `src/lib/payments/` integrates with Stripe

## HOW (Commands & Verification)
- Build: `bun run build`
- Test: `bun test`
- Lint: `bun run lint`
- Type check: `bun run typecheck`

## Rules
- Use Bun, not npm/yarn
- Prefer server components; use 'use client' only when necessary
- All API routes require authentication middleware
```

### What NOT to Include

- **Code snippets** - They go stale. Use `file:line` references instead
- **Style guides** - Let linters handle formatting (Prettier, ESLint, etc.)
- **Obvious information** - Don't explain what React is
- **Everything** - If it's rarely relevant, it doesn't belong here

### Use Pointers, Not Copies

```markdown
# Bad
Here's our auth middleware:
\`\`\`typescript
export function authMiddleware(req: Request) {
  // 50 lines of code that will become outdated
}
\`\`\`

# Good
Auth middleware: see `src/middleware/auth.ts:15`
```

### Iterate Over Time

Use the `#` key during sessions to add instructions you find yourself repeating. These get appended to CLAUDE.md and accumulate into a file that reflects how your team actually works.

---

## Slash Commands

Custom commands you invoke manually via `/command`. Great for repeatable workflows.

### Location

```
.claude/commands/          # Project commands (shared via git)
~/.claude/commands/        # Personal commands (all projects)
```

### File Format

Create a Markdown file with your prompt template:

```markdown
<!-- .claude/commands/fix-issue.md -->
Fix GitHub issue #$ARGUMENTS

1. Read the issue details using gh cli
2. Understand the problem
3. Implement the fix
4. Write tests
5. Create a PR
```

Usage: `/project:fix-issue 1234`

### Examples

**Debug Loop** (`.claude/commands/debug.md`):
```markdown
Debug the following error: $ARGUMENTS

1. Identify the error source
2. Check related files
3. Propose and implement a fix
4. Verify the fix works
```

**Code Review** (`.claude/commands/review.md`):
```markdown
Review the changes in this PR for:
- Security vulnerabilities
- Performance issues
- Code style consistency
- Missing tests

PR: $ARGUMENTS
```

---

## Subagents

Isolated Claude instances that handle specific tasks independently, returning only results to your main conversation.

### Why Use Subagents?

- **Context isolation**: Heavy research doesn't pollute your main thread
- **Parallel execution**: Multiple subagents can work simultaneously
- **Specialization**: Each gets its own system prompt and tool access

### Location

```
.claude/agents/           # Project subagents
```

### File Format

```markdown
<!-- .claude/agents/security-reviewer.md -->
---
name: security-reviewer
description: Reviews code for security vulnerabilities and OWASP top 10 issues
tools:
  - Read
  - Grep
  - Glob
---

You are a security-focused code reviewer. Analyze code for:
- Injection vulnerabilities (SQL, command, XSS)
- Authentication/authorization flaws
- Sensitive data exposure
- Security misconfigurations

Report findings with severity levels and remediation steps.
```

### YAML Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Identifier for the subagent |
| `description` | Yes | What triggers this subagent (Claude matches tasks to this) |
| `tools` | No | Allowed tools (defaults vary by type) |
| `model` | No | Override model (sonnet, opus, haiku) |

### Built-in Subagent Types

Claude Code includes several built-in subagents:
- **Explore**: Fast codebase exploration
- **Plan**: Implementation planning
- **Bash**: Command execution

### Important Limitations

- Subagents **cannot spawn other subagents**
- If you need nested delegation, use Skills instead

---

## Skills

Auto-discovered capabilities that Claude applies when it recognizes matching tasks. Unlike slash commands, you don't invoke these manually.

### Location

```
.claude/skills/skill-name/
  SKILL.md              # Required: metadata + instructions
  patterns/             # Optional: example patterns
  templates/            # Optional: code templates
  scripts/              # Optional: helper scripts
```

### SKILL.md Format

```markdown
<!-- .claude/skills/dexie-db/SKILL.md -->
---
name: dexie-database
description: Implements IndexedDB operations using Dexie.js
---

# Dexie.js Database Skill

Before implementing any Dexie.js database code:
1. Fetch current docs: https://dexie.org/docs/
2. Check existing schema in `src/db/schema.ts`

## Patterns
See `patterns/` for common query patterns.

## Rules
- Always use transactions for multiple operations
- Define explicit TypeScript types for tables
```

### Progressive Disclosure

Skills use a three-level loading system to manage context:

1. **Level 1**: Only `name` and `description` loaded at startup
2. **Level 2**: Full `SKILL.md` content loaded when relevant
3. **Level 3+**: Additional files loaded only when needed

This means skills can contain unbounded amounts of context without overwhelming Claude's context window.

### Skills vs Slash Commands

| Aspect | Skills | Slash Commands |
|--------|--------|----------------|
| Invocation | Automatic (Claude decides) | Manual (`/command`) |
| Structure | Directory with files | Single Markdown file |
| Arguments | No | Yes (`$ARGUMENTS`) |
| Best for | Rich, multi-file workflows | One-shot operations |

---

## Hooks

Shell commands that execute automatically at specific points in Claude's lifecycle.

### Configuration Location

```json
// ~/.claude/settings.json (user)
// .claude/settings.json (project)
// .claude/settings.local.json (local, gitignored)
```

### Hook Events

| Event | When It Fires |
|-------|---------------|
| `PreToolUse` | Before a tool executes |
| `PostToolUse` | After a tool completes successfully |
| `Notification` | When Claude sends a notification |
| `Stop` | When Claude finishes responding |
| `UserPromptSubmit` | When user submits a prompt |
| `SessionStart` | When a session begins |

### Configuration Format

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "prettier --write $CLAUDE_FILE_PATHS"
          }
        ]
      }
    ]
  }
}
```

### Matcher Patterns

- `"Write"` - Exact match
- `"Write|Edit"` - Multiple tools
- `"*"` - All tools

### Environment Variables

| Variable | Description |
|----------|-------------|
| `$CLAUDE_FILE_PATHS` | Space-separated list of affected files |
| `$CLAUDE_TOOL_OUTPUT` | Tool output (PostToolUse only) |

### Common Use Cases

**Auto-format on edit:**
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [{
          "type": "command",
          "command": "prettier --write $CLAUDE_FILE_PATHS"
        }]
      }
    ]
  }
}
```

**Log all bash commands:**
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [{
          "type": "command",
          "command": "jq -r '.tool_input.command' >> ~/.claude/command-log.txt"
        }]
      }
    ]
  }
}
```

**Run tests after code changes:**
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [{
          "type": "command",
          "command": "npm test --findRelatedTests $CLAUDE_FILE_PATHS"
        }]
      }
    ]
  }
}
```

---

## MCP Servers

Model Context Protocol servers extend Claude's capabilities with external tools and services.

### Configuration Methods

**CLI:**
```bash
claude mcp add github --scope project
claude mcp list
claude mcp remove github
```

**Interactive:**
```
/mcp
```

### Configuration Files

| Scope | Location | Shared |
|-------|----------|--------|
| User | `~/.claude.json` | No |
| Project | `.mcp.json` (root) | Yes (via git) |
| Local | `.claude/.mcp.json` | No |

### Example .mcp.json

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
      }
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "${DATABASE_URL}"
      }
    },
    "puppeteer": {
      "command": "npx",
      "args": ["-y", "@anthropic/mcp-server-puppeteer"]
    }
  }
}
```

### Popular MCP Servers

- **GitHub**: PR management, issues, code search
- **Puppeteer**: Browser automation, screenshots
- **PostgreSQL/MySQL**: Database operations
- **Slack**: Team notifications
- **Sentry**: Error tracking

---

## Settings & Permissions

### Settings Files Hierarchy

```
~/.claude/settings.json          # User (lowest priority)
.claude/settings.json            # Project (shared)
.claude/settings.local.json      # Local (highest priority, gitignored)
```

### Managing Permissions

**Interactive:**
```
/permissions
```

**CLI:**
```bash
claude config set allowedTools '["Bash(npm:*)", "Read", "Write"]'
```

### Permission Format

```json
{
  "permissions": {
    "allow": [
      "Read",
      "Write",
      "Edit",
      "Bash(npm:*)",
      "Bash(git:*)",
      "Bash(bun:*)"
    ],
    "deny": [
      "Bash(rm -rf:*)",
      "Bash(sudo:*)"
    ]
  }
}
```

---

## Workflow Tips

### The Explore-Plan-Code-Commit Cycle

1. **Explore**: Ask Claude to read files without writing code
2. **Plan**: Use extended thinking ("think hard") for complex planning
3. **Code**: Implement with verification steps
4. **Commit**: Request commit and PR creation

### Extended Thinking Triggers

| Phrase | Thinking Depth |
|--------|----------------|
| "think" | Standard |
| "think hard" | Extended |
| "think harder" | More extended |
| "ultrathink" | Maximum |

### Plan Mode

Press `Shift+Tab` twice to enter Plan Mode. Claude becomes a research/analysis machine that cannot modify files until you approve.

### Context Management

- Use `/clear` between unrelated tasks
- Use subagents for heavy research to keep main context clean
- Press `Escape` to interrupt and preserve context
- Double-tap `Escape` to edit previous prompts

### Parallel Workflows

- Run multiple Claude instances in separate terminal tabs
- Use git worktrees for independent task separation
- Have one Claude write code while another reviews

### Test-Driven Development with Claude

1. Have Claude write tests based on expected behavior
2. Confirm tests fail
3. Commit tests
4. Have Claude implement code to pass tests
5. Use a separate subagent to verify no overfitting

---

## When to Use What

| Use Case | Tool |
|----------|------|
| Always-true project rules | CLAUDE.md |
| On-demand repeatable workflow | Slash Command |
| Heavy research/analysis | Subagent |
| Auto-applicable rich workflow | Skill |
| Code formatting/linting | Hook |
| External service integration | MCP Server |

### Decision Tree

```
Need project context always loaded?
  └─ Yes → CLAUDE.md

Need to invoke manually with arguments?
  └─ Yes → Slash Command

Need isolated context for complex research?
  └─ Yes → Subagent

Need auto-triggered workflow with supporting files?
  └─ Yes → Skill

Need to run code before/after Claude actions?
  └─ Yes → Hook

Need external tool/service access?
  └─ Yes → MCP Server
```

---

## Quick Start Checklist

- [ ] Create a concise `CLAUDE.md` with WHAT/WHY/HOW
- [ ] Set up `.claude/settings.json` with basic permissions
- [ ] Add common commands to `.claude/commands/`
- [ ] Configure hooks for auto-formatting
- [ ] Add relevant MCP servers to `.mcp.json`
- [ ] Create subagents for specialized tasks

---

## Sources

- [Claude Code: Best practices for agentic coding](https://www.anthropic.com/engineering/claude-code-best-practices) - Anthropic
- [Writing a good CLAUDE.md](https://www.humanlayer.dev/blog/writing-a-good-claude-md) - HumanLayer
- [Claude Code customization guide](https://alexop.dev/posts/claude-code-customization-guide-claudemd-skills-subagents/) - alexop.dev
- [Create custom subagents](https://code.claude.com/docs/en/sub-agents) - Claude Code Docs
- [Equipping agents with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) - Anthropic
- [Get started with hooks](https://code.claude.com/docs/en/hooks-guide) - Claude Code Docs
- [Connect Claude Code to MCP](https://code.claude.com/docs/en/mcp) - Claude Code Docs
- [Claude Code settings](https://code.claude.com/docs/en/settings) - Claude Code Docs
- [CLAUDE.md best practices](https://arize.com/blog/claude-md-best-practices-learned-from-optimizing-claude-code-with-prompt-learning/) - Arize
