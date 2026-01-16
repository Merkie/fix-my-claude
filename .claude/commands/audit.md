# Audit Repository Claude Setup

Target repository: $ARGUMENTS

Perform a quick health check of the Claude setup without making any changes.

## Checks to Perform

1. **CLAUDE.md Exists?**
   - Location: root or .claude/
   - If missing: "No CLAUDE.md found"

2. **CLAUDE.md Length**
   - Count lines
   - Grade:
     - <50 lines: "Minimal"
     - 50-150 lines: "Good"
     - 150-300 lines: "Long - consider trimming"
     - >300 lines: "Too long - needs refactoring"

3. **CLAUDE.md Content Quality**
   - Has build command? [Yes/No]
   - Has test command? [Yes/No]
   - Has lint command? [Yes/No]
   - Contains code snippets? [Yes/No - bad if yes]
   - Contains style guide? [Yes/No - bad if yes, should use linter]

4. **.claude/ Folder**
   - Exists? [Yes/No]
   - Has commands/? [Count]
   - Has agents/? [Count]
   - Has skills/? [Count]
   - Has settings.json? [Yes/No]

5. **MCP Configuration**
   - .mcp.json exists? [Yes/No]
   - Servers configured: [List]

6. **Linter/Formatter Setup**
   - Has .eslintrc*? [Yes/No]
   - Has .prettierrc*? [Yes/No]
   - Has biome.json? [Yes/No]
   - Has other linter config? [List]

## Output Format

```
## Claude Setup Audit: [repo-name]

### Health Score: [X/10]

### CLAUDE.md
- Status: [Found/Missing]
- Location: [path]
- Lines: [count] ([grade])
- Build command: [Yes/No]
- Test command: [Yes/No]
- Lint command: [Yes/No]

### Issues
- [ ] [Issue 1]
- [ ] [Issue 2]

### .claude/ Configuration
- Commands: [count]
- Agents: [count]
- Skills: [count]
- Settings: [Yes/No]

### External Tooling
- Linter: [Yes/No] ([which])
- Formatter: [Yes/No] ([which])
- MCP Servers: [count]

### Recommendations
1. [Most important fix]
2. [Second priority]
3. [Third priority]

Run `/project:fix [repo-path]` to automatically fix these issues.
```

## Do NOT

- Make any changes
- Create any files
- Modify any configurations

This is read-only analysis.
