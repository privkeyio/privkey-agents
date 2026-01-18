# privkey-agents

Surgical workflow agents for Claude Code. Language-agnostic agents for precise code execution, PR review, PR fixes, and refactoring.

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
```
**File:** `src/auth.ts`
**Line:** 42-45
**Code:**
```ts
if (user.isAdmin) {
  grantAccess();
}
```
**Comment:** Missing null check on user object before accessing isAdmin
**Severity:** Blocker
```

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

## Author

privkey

## Version

1.0.0
