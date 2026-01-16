# Fix Repository Claude Setup

Target repository: $ARGUMENTS

**Reference docs (read if needed):**
- `.claude/docs/templates.md` - Output templates
- `.claude/docs/best-practices.md` - Detailed guidance

Parse the arguments to extract:
- `repo-path`: The path to the repository to fix (required)
- `--simple` or `--complex`: Setup complexity (default: --simple)

## Phase 1: Analysis

First, analyze the target repository without making changes:

1. **Check for existing CLAUDE.md**
   - Read the file if it exists
   - Note: location, line count, content structure
   - Identify red flags: code snippets, style guides, excessive length

2. **Check for .claude/ folder**
   - List contents: commands/, agents/, skills/, settings.json
   - Read any existing configurations

3. **Check for .mcp.json**
   - Read if exists, note configured servers

4. **Identify project type**
   - Read package.json, Cargo.toml, go.mod, pyproject.toml, etc.
   - Determine: language, framework, build system, test runner

5. **Find existing tooling configs**
   - Linters: .eslintrc*, .prettierrc*, biome.json, ruff.toml, etc.
   - Check if formatting/style is already handled by tools

6. **Assess project structure**
   - Is it a monorepo? Check for workspaces, packages/, apps/
   - Identify key directories and their purposes

## Phase 2: Report Findings

Present a summary to the user:

```
## Analysis Results

**Current State:**
- CLAUDE.md: [exists/missing] ([X] lines)
- .claude/ folder: [exists/missing]
- Setup complexity: [none/basic/advanced]

**Issues Found:**
- [List specific problems]

**Recommended Changes:**
- [List proposed fixes]

**Setup Type:** [Simple/Complex based on flag]
```

Ask the user to confirm before proceeding.

## Phase 3: Implementation

### If --simple (default):

Create a focused CLAUDE.md with:
- Project name and one-line description
- Tech stack (framework, language, key dependencies)
- Key directories with purposes
- Essential commands: build, test, lint, typecheck
- 3-5 critical rules/conventions

Create 1-3 essential commands in .claude/commands/:
- A debug or troubleshoot command if relevant
- A PR/commit workflow if not obvious
- Project-specific workflow if identified

Optionally add .claude/settings.json with:
- Basic allowed tools
- PostToolUse hook for formatting (if formatter exists)

### If --complex:

Create a lean CLAUDE.md (orchestration focused):
- Project overview (brief)
- Architecture map
- Available subagents and when to use them
- Core commands reference
- Critical invariants only

Create specialized subagents in .claude/agents/:
- code-reviewer.md - For PR reviews and code quality
- test-writer.md - For generating tests
- debugger.md - For investigating issues
- (Others based on project needs)

Create commands in .claude/commands/:
- Common workflows extracted from bloated CLAUDE.md
- Orchestration commands that leverage subagents

Create .claude/settings.json with:
- Granular permissions
- Hooks for formatting, linting, type-checking

Create .mcp.json if beneficial:
- GitHub server if heavy PR workflow
- Database server if data-heavy project

## Phase 4: Verification

After changes:
1. Confirm new CLAUDE.md is under 150 lines (simple) or 80 lines (complex)
2. List all created files
3. Show the user the new structure
4. Suggest they test with a simple task

## Important

- Always ask for confirmation before making changes
- Preserve any valuable content from existing CLAUDE.md
- Don't delete existing commands/agents without replacement
- If uncertain about project conventions, ask the user
