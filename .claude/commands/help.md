# Help

Display usage information for fix-my-claude.

## Output

```
# fix-my-claude

Audits and fixes repos with bloated CLAUDE.md files or messy Claude Code configurations.

## Commands

/audit <path>             Read-only health check of Claude setup
/fix <path>               Fix issues (default: simple setup)
/fix <path> --complex     Fix with full subagentic architecture
/migrate <path> --to-simple    Convert to simple setup
/migrate <path> --to-complex   Convert to complex setup
/help                     Show this help message

## Examples

# Check a repo's Claude setup health
/audit ~/my-project

# Fix with recommended simple setup
/fix ~/my-project

# Fix with complex multi-agent setup
/fix ~/my-project --complex

# Convert an over-engineered setup to simple
/migrate ~/my-project --to-simple

## What Gets Fixed

- Bloated CLAUDE.md files (>150 lines)
- Missing build/test/lint commands
- Code snippets that go stale
- Style guides (should be in linter configs)
- Messy .claude/ folder structure

## Reference

See how-to-claude.md for the full Claude Code setup guide.
```

Print the above help text exactly as shown.
