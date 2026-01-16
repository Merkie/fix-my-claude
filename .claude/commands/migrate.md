# Migrate Claude Setup Complexity

Target repository: $ARGUMENTS

Parse arguments:
- `repo-path`: Repository to migrate
- `--to-simple` or `--to-complex`: Migration direction (required)

## Migrate to Simple (--to-simple)

Consolidate a complex setup into a simpler one:

1. **Inventory existing setup**
   - Read all subagents in .claude/agents/
   - Read all commands in .claude/commands/
   - Read all skills in .claude/skills/
   - Read current CLAUDE.md

2. **Consolidate subagent knowledge**
   - Extract essential rules from each subagent
   - Merge into CLAUDE.md as concise guidelines
   - Keep only universally applicable content

3. **Consolidate commands**
   - Keep only 2-3 most frequently used commands
   - Inline simple commands into CLAUDE.md "Commands" section
   - Archive or delete rarely-used commands

4. **Create unified CLAUDE.md**
   - Target: 50-150 lines
   - Include all essential project context
   - Remove references to deleted subagents/commands

5. **Cleanup**
   - Ask user before deleting any agents/skills
   - Keep commands/ folder with essential commands only
   - Simplify settings.json

## Migrate to Complex (--to-complex)

Expand a simple setup into a subagentic architecture:

1. **Analyze current CLAUDE.md**
   - Identify sections that could be subagents
   - Identify workflows that could be commands
   - Find content that varies by task type

2. **Extract subagents**
   - Code review guidelines → .claude/agents/code-reviewer.md
   - Testing instructions → .claude/agents/test-writer.md
   - Debugging procedures → .claude/agents/debugger.md
   - Domain-specific knowledge → .claude/agents/[domain].md

3. **Extract commands**
   - Multi-step workflows → .claude/commands/[workflow].md
   - Numbered procedures → .claude/commands/[procedure].md

4. **Slim down CLAUDE.md**
   - Target: 30-80 lines
   - Keep only: project overview, architecture, command reference
   - Add: subagent directory with when-to-use guidance

5. **Enhance settings.json**
   - Add formatting hooks
   - Configure appropriate permissions
   - Add linting hooks if relevant

## Output

Show user:
- Before/after comparison
- List of files created/modified/deleted
- New structure diagram

## Confirmation Required

Always ask user to confirm before:
- Deleting any existing files
- Removing content from CLAUDE.md
- Creating new subagents

Preserve original files with .backup extension if user requests.
