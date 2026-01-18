# privkey-agents

Surgical workflow agents for Claude Code. Language-agnostic agents for precise code execution, PR review, PR fixes, refactoring, and stress testing.

## Overview

These agents enforce a disciplined approach to code changes:
- **Surgical**: Make only necessary changes, nothing more
- **Verified**: Confirm work is actually done, don't assume
- **Clean**: No unnecessary comments in the codebase
- **Language-agnostic**: Works with any codebase

## Agents

### `code-execution`

Surgically execute code changes with mandatory verification.

**When to use:**
- Implementing features
- Fixing bugs
- Making targeted code modifications

**What it does:**
1. Reads and understands the target code
2. Plans minimal changes required
3. Makes precise, targeted edits
4. Runs tests/build to verify
5. Confirms the change actually works

**Usage:**
```
Implement user authentication - make sure it actually works
```

### `pr-review`

Deep audit PR changes and generate high-value review comments.

**When to use:**
- Reviewing pull requests before merge
- Auditing code changes for issues
- Generating PR feedback

**What it does:**
1. Analyzes all changes in the PR branch
2. Audits for bugs, security issues, missing error handling
3. Generates comments with specific line numbers

**Output format:**

> **File:** `src/auth.ts`
> **Line:** 42-45
> **Code:**
> ```ts
> if (user.isAdmin) {
>   grantAccess();
> }
> ```
> **Comment:** Missing null check on user object before accessing isAdmin
> **Severity:** Blocker

**Usage:**
```
Review this PR branch for issues before I merge it
```

### `pr-fixes`

Rebase PR on main and surgically fix issues for production.

**When to use:**
- Preparing a PR branch for merge
- Fixing PR review feedback
- Getting a branch production-ready

**What it does:**
1. Rebases on main with signing (`git rebase -S`)
2. Deep audits all changes
3. Fixes issues surgically
4. Verifies tests/build pass

**Usage:**
```
Get this PR ready to merge
```

### `refactor`

Refactor code for simplicity and keep files under 500 lines.

**When to use:**
- Cleaning up and organizing code
- Splitting large files
- Improving code structure

**What it does:**
1. Finds files over 500 lines
2. Evaluates if splitting makes sense
3. Refactors for simplicity
4. Verifies nothing broke

**Usage:**
```
Refactor the src/services directory - files are getting too long
```

### `stress-test`

Try to crash code and find memory issues, overflows, segfaults, and security vulnerabilities.

**When to use:**
- Hardening code before release
- Finding edge cases and crash conditions
- Security testing and fuzzing
- Memory leak and corruption detection

**What it does:**
1. Identifies attack surface (input handlers, memory ops, etc.)
2. Runs static analysis and sanitizers
3. Fuzzes inputs and tests edge cases
4. Tests for security vulnerabilities (injection, auth bypass)
5. Fixes issues surgically with verification

**Language-specific support:**
- **C**: Buffer overflow detection, sanitizers (ASan, UBSan), valgrind, dangerous function audit
- **Rust**: Unsafe code audit, Miri for UB detection, panic-across-FFI checks, clippy lints

**Usage:**
```
Stress test the parser module - try to crash it and find any memory issues
```

### `security-review`

Review code for security vulnerabilities including OWASP top 10.

**When to use:**
- After implementing auth/authorization code
- Adding user input handling or form processing
- Working with database queries or external APIs
- Before merging PRs with security-sensitive changes

**What it does:**
1. Identifies changed files in the branch
2. Scans for hardcoded secrets and credentials
3. Checks for injection vulnerabilities (SQL, command, path traversal)
4. Reviews auth/authorization patterns
5. Audits for XSS, cryptographic issues, memory safety

**Output format:**
Findings grouped by severity (Critical, High, Medium, Low) with specific file paths, line numbers, and fix recommendations.

**Usage:**
```
Review this branch for security issues before I merge
```

## Installation

Clone the repo:
```bash
git clone https://github.com/privkeyio/privkey-agents.git ~/privkey-agents
```

Add to your `~/.claude/settings.json` (replace `YOUR_USERNAME` with your actual username):
```json
{
  "extraKnownMarketplaces": {
    "privkey-agents": {
      "source": {
        "source": "directory",
        "path": "/home/YOUR_USERNAME/privkey-agents"
      }
    }
  },
  "enabledPlugins": {
    "privkey-agents@privkey-agents": true
  }
}
```

Restart Claude Code to load the plugin.

### Quick test (without installing)

```bash
git clone https://github.com/privkeyio/privkey-agents.git
claude --plugin-dir ./privkey-agents
```

## Best Practices

- **Let agents verify**: Don't skip verification steps - they catch real issues
- **Trust the surgical approach**: Minimal changes = fewer bugs
- **Use pr-review before merge**: Catches issues before they hit production
- **Refactor incrementally**: Don't try to refactor everything at once

## When to Use

**Use these agents for:**
- Features requiring careful implementation
- PR reviews where quality matters
- Codebases where you want minimal, verified changes
- Hardening code and finding vulnerabilities before release

**Don't use for:**
- Quick exploratory changes
- Throwaway prototypes
- When you explicitly want broad refactoring

## Requirements

- Claude Code installed
- Git repository (for PR agents)

## Global Claude Configuration

Create `~/.claude/CLAUDE.md` to set project-agnostic instructions for Claude. See [CLAUDE.md.example](CLAUDE.md.example) for a starting point:

```bash
mkdir -p ~/.claude
cp CLAUDE.md.example ~/.claude/CLAUDE.md
```

Claude reads this file for all projects, applying your preferences globally.

## Adding New Agents

1. Create a new `.md` file in `privkey-agents/agents/` with frontmatter:
   ```yaml
   ---
   name: agent-name
   description: "Description for Claude to know when to use this agent"
   model: opus
   tools:
     - Glob
     - Grep
     - Read
     - Edit
     - Write
     - Bash
   ---
   ```

2. **Bump the version** in `privkey-agents/.claude-plugin/plugin.json` - Claude Code won't pick up new agents without a version change

3. Run `claude plugins uninstall privkey-agents && claude plugins install ./privkey-agents` or select "Update now" from the plugin menu

## Author

privkey

## Version

1.2.0
