# Claude Code Best Practices Reference

Detailed guidance for fixing Claude setups. Read when you need deeper context.

---

## The 150-200 Instruction Limit

Research shows frontier LLMs reliably follow ~150-200 instructions. Beyond that:
- Earlier instructions get deprioritized ("context drift")
- Claude may ignore or contradict rules
- Performance degrades

**Measuring instructions:**
- Each bullet point ≈ 1 instruction
- Each rule ≈ 1 instruction
- Complex rules with multiple clauses ≈ 2-3 instructions

**Target line counts:**
- Simple setup: 50-150 lines
- Complex setup: 30-80 lines (rest in subagents/commands)

---

## WHAT/WHY/HOW Framework

Every good CLAUDE.md answers three questions:

### WHAT (Tech Stack & Structure)
- Languages and versions
- Frameworks
- Key dependencies
- Directory structure with purposes
- Important files

### WHY (Purpose & Context)
- What the project does
- Why architectural decisions were made
- What each major component is for

### HOW (Commands & Verification)
- How to install dependencies
- How to build
- How to run tests
- How to lint/format
- How to verify changes work

---

## Red Flags & How to Fix Them

### 1. CLAUDE.md > 300 lines
**Problem:** Too much for Claude to reliably follow
**Fix:**
- Extract workflows → .claude/commands/
- Extract personas → .claude/agents/
- Remove redundant info
- Use file:line references instead of code

### 2. Code Snippets in CLAUDE.md
**Problem:** Go stale immediately when code changes
**Fix:**
- Replace with `see src/utils/auth.ts:45`
- Or just describe the pattern without code

### 3. Style Guides in CLAUDE.md
**Problem:** LLMs are slow and expensive for style enforcement
**Fix:**
- Move to .eslintrc, .prettierrc, biome.json, etc.
- Add PostToolUse hook to auto-format

### 4. No Build/Test Commands
**Problem:** Claude has to figure out the build system every time
**Fix:**
- Add explicit commands section
- Include all: build, test, lint, typecheck

### 5. Everything in One File
**Problem:** Monolithic CLAUDE.md becomes unmaintainable
**Fix:**
- Commands → .claude/commands/
- Subagents → .claude/agents/
- Reference docs → .claude/docs/ or skills

### 6. Duplicate Information
**Problem:** Conflicting info, wasted context
**Fix:**
- Pick canonical location
- Reference from other places

### 7. Vague Instructions
**Problem:** "Run the tests" - which command?
**Fix:**
- Be specific: `bun test` not "run tests"
- Include flags if needed: `bun test --coverage`

---

## What Goes Where

| Content Type | Best Location | Why |
|--------------|---------------|-----|
| Project description | CLAUDE.md | Always needed |
| Tech stack | CLAUDE.md | Always needed |
| Build/test commands | CLAUDE.md | Always needed |
| Critical rules (3-5) | CLAUDE.md | Always needed |
| Style rules | Linter config | Deterministic, fast |
| Multi-step workflows | .claude/commands/ | On-demand, keeps CLAUDE.md lean |
| Specialized personas | .claude/agents/ | Isolated context |
| Rich auto-workflows | .claude/skills/ | Progressive disclosure |
| External integrations | .mcp.json | Separate concern |
| Auto-formatting | hooks in settings.json | Automatic |

---

## Simple vs Complex: Decision Guide

### Choose Simple When:
- Project has < 50 files
- Solo developer or small team
- Team is new to Claude Code
- Straightforward tech stack
- No need for specialized review workflows

### Choose Complex When:
- Large codebase (100+ files)
- Monorepo structure
- Team heavily uses Claude Code daily
- Need specialized reviewers (security, perf, etc.)
- Multiple distinct workflows (debug, deploy, review)
- Want to leverage parallel subagents

---

## Common Workflows to Extract as Commands

1. **Debug workflow** - "When debugging, first check X, then Y..."
2. **PR review workflow** - "Before merging, verify A, B, C..."
3. **New feature workflow** - "When adding features, create test first..."
4. **Deploy workflow** - "To deploy, run X then Y..."
5. **Incident response** - "When investigating bugs..."

---

## Common Subagents for Complex Setups

1. **code-reviewer** - PR reviews, code quality
2. **test-writer** - Generate test cases
3. **debugger** - Investigate issues
4. **security-reviewer** - Security audit
5. **performance-analyzer** - Perf analysis
6. **documentation-writer** - Generate docs (only if team wants this)

---

## Hook Patterns

### Auto-format on edit
```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{"type": "command", "command": "prettier --write $CLAUDE_FILE_PATHS"}]
    }]
  }
}
```

### Type-check after changes
```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{"type": "command", "command": "tsc --noEmit"}]
    }]
  }
}
```

### Run related tests
```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{"type": "command", "command": "jest --findRelatedTests $CLAUDE_FILE_PATHS"}]
    }]
  }
}
```

---

## Quality Checklist for Output

Before finishing a fix, verify:

- [ ] CLAUDE.md under line limit (150 simple, 80 complex)
- [ ] No code snippets in CLAUDE.md
- [ ] No style guides in CLAUDE.md (use linter)
- [ ] All commands are concrete (`bun test` not "run tests")
- [ ] Build, test, lint commands all present
- [ ] Rules are actionable and necessary
- [ ] Structure is scannable (headers, bullets, tables)
- [ ] No duplicate information across files
- [ ] Commands are documented in CLAUDE.md
- [ ] Subagents (if any) have clear triggers
