# Privkey Agents Plugin

Surgical workflow agents for Claude Code. Built for precision, verification, and production-readiness.

## Overview

Privkey Agents provides specialized agents for common software engineering workflows. Each agent is designed to do one thing well: execute surgically, verify completely, and produce production-ready results.

## Agents

### `code-execution`

Execute code changes surgically with mandatory verification.

**What it does:**
- Reads and fully understands target code before making changes
- Plans minimal, precise modifications
- Makes targeted edits following existing patterns
- Runs formatter, build, and tests to verify
- Confirms the change actually works before completing

**When to use:**
- Implementing new features
- Fixing bugs
- Making targeted code modifications
- Any task requiring verified completion

**Usage:**
```
"Implement user authentication"
"Fix the null pointer bug in parser.ts"
"Add rate limiting to the API endpoint"
```

**Triggers:** implement, fix, change, update, add feature, modify code, surgical

---

### `pr-review`

Deep audit of PR changes to find production and security blockers.

**What it does:**
- Analyzes all changes in the PR branch
- Reads every modified file completely (not just diffs)
- Audits for bugs, security issues, resource leaks
- Generates concise, actionable review comments with code snippets

**When to use:**
- Before merging any PR
- Running in background during development
- Code quality gate in CI

**Usage:**
```
"Review this PR for issues"
"Run pr-review in background"
```

**Output:**
```
## PR Review: [Brief description]

---

**1. [Issue title]**

`src/file.ts:42`
```ts
problematic_line();
```

[1-2 sentences - what to fix and why]

---

### Summary

1. [Action item]
2. [Action item]
```

**Focus areas:**
- Memory leaks on error paths (try/?/throw skips cleanup)
- Logic bugs (dead code, magic numbers, removed tests)
- Security issues (injection, auth bypass, missing validation, DoS)

---

### `pr-fixes`

Rebase PR on main and surgically fix issues for production.

**What it does:**
- Rebases on main with signing (`git rebase -S origin/main`)
- Deep audits all changes for bugs and security issues
- Fixes issues surgically, preserving author intent
- Verifies formatter, tests, and build pass
- Asks for confirmation before pushing

**When to use:**
- Preparing a PR branch for merge
- Getting a branch production-ready
- Fixing PR review feedback

**Usage:**
```
"Get this PR ready to merge"
"Fix PR issues and rebase on main"
```

**Output:**
```
## PR Assessment Summary

**Ready to merge?** Yes/No

**High-value contribution?** Yes/No - Brief reasoning

### Fixes Made During Review
- List of fixes (or "None needed")

### Verification
- Test results
- Build results
```

---

### `refactor`

Refactor code for simplicity and keep files under 500 lines.

**What it does:**
- Finds files over 500 lines
- Evaluates if splitting makes sense (doesn't force splits)
- Refactors for simplicity and clarity
- Removes dead code, improves naming
- Verifies tests and build pass

**When to use:**
- Cleaning up large files
- Improving code organization
- After completing a feature

**Usage:**
```
"Refactor for simplicity"
"Split large files in this module"
```

**Decision framework:**
- **Split when:** Multiple responsibilities, parts change independently, parts could be reused
- **Keep together when:** Cohesive code, splitting requires many parameters, tightly coupled

---

### `stress-test`

Find crashes, memory issues, overflows, segfaults, and security vulnerabilities.

**What it does:**
- Identifies attack surface (input handlers, memory operations)
- Runs static analysis and sanitizers
- Fuzzes inputs and tests edge cases
- Tests security attack vectors
- Fixes issues surgically with verification

**When to use:**
- Hardening security-critical code
- Before releasing parsers or input handlers
- Finding edge cases in new features

**Usage:**
```
"Stress test the parser module"
"Find crashes and memory issues"
"Break this code intentionally"
```

**Language support:**
- **C/C++:** Buffer overflow detection, ASan, UBSan, valgrind, cppcheck
- **Rust:** Unsafe code audit, Miri for UB, clippy, cargo fuzz
- **Go:** Race detector, go vet, staticcheck, fuzz testing
- **Python:** Bandit security scanner, pylint
- **JS/TS:** ESLint security rules

**What it tests:**
- Numeric boundaries (0, -1, INT_MAX, NaN)
- String edge cases (empty, huge, null bytes, unicode)
- Injection attacks (SQL, command, path traversal)
- Resource exhaustion and DoS vectors
- Race conditions and concurrency bugs

---

### `security-review`

Review code for security vulnerabilities including OWASP top 10.

**What it does:**
- Identifies changed files in the branch
- Scans for hardcoded secrets and credentials
- Checks for injection vulnerabilities
- Reviews auth/authorization patterns
- Audits for XSS, crypto issues, memory safety, DoS vectors

**When to use:**
- After implementing auth or security features
- Before merging security-sensitive PRs
- Regular security audits

**Usage:**
```
"Run security review on this branch"
"Check for OWASP vulnerabilities"
```

**Output:**
```
## Security Review Results

### Critical

**Hardcoded API Key**
- File: `src/api/client.ts:15`
- Risk: Credentials exposed in source code
- Fix: Use environment variables

## Summary
- Critical: 1
- High: 0
- Medium: 0
- Low: 0
```

**Severity levels:**
- **Critical:** RCE, SQL injection, auth bypass, exposed secrets
- **High:** XSS, IDOR, weak crypto, sensitive data exposure
- **Medium:** Missing security headers, verbose errors
- **Low:** Missing audit logging, minor config issues

## Commands

### `/pr-pipeline`

Execute a multi-stage pipeline to prepare code for PR merge.

**What it does:**
1. **Stage 1:** `pr-fixes` - Rebase on main, audit and fix PR issues
2. **Stage 2:** `security-review` - Security audit of the fixed code
3. **Stage 3:** `code-execution` - Fix any security issues identified
4. **Stage 4:** `code-simplifier` - Clean up and simplify the code
5. **Final:** Recap all changes and ask for confirmation before push

**When to use:**
- Full verification before important merges
- Comprehensive PR preparation
- When you want all checks in one workflow

**Usage:**
```
/pr-pipeline
```

## Workflow Examples

### Quick implementation:
```
"Implement user authentication"
```
Triggers `code-execution` - makes changes and verifies they work.

### PR preparation:
```
/pr-pipeline
```
Runs full pipeline: rebase, audit, fix, simplify, confirm.

### Security check:
```
"Run security review on this branch"
```
Launches `security-review` to audit for vulnerabilities.

### Stress testing:
```
"Stress test the parser module"
```
Launches `stress-test` to find crashes and edge cases.

### Background review:
```
"Review this PR in background"
```
Runs `pr-review` asynchronously while you continue working.

## Best Practices

- **Use `pr-review` before merging** - Catches bugs and security issues early
- **Run `/pr-pipeline` for important PRs** - Full verification before merge
- **Use `stress-test` for input handling code** - Find edge cases before production
- **Let agents verify their work** - Don't skip verification steps
- **Run `security-review` for auth code** - Catch vulnerabilities early

## Requirements

- Claude Code installed
- Git repository (for PR-related agents)
- Project-specific tools (formatters, test runners, build systems)

## Troubleshooting

### Agent doesn't trigger

**Issue:** Agent doesn't start when expected

**Solution:**
- Check trigger words match the agent description
- Use explicit phrasing like "Run stress-test agent" or "Launch pr-review"

### Verification fails

**Issue:** Agent reports test or build failures

**Solution:**
- Fix the underlying issues first
- Agents verify their work - failures indicate real problems
- Check project has proper test/build commands configured

### Rebase conflicts

**Issue:** `pr-fixes` encounters merge conflicts

**Solution:**
- The agent will attempt to resolve conflicts surgically
- For complex conflicts, resolve manually then re-run
- Ensure your branch is based on a recent main

## Author

Privkey (hello@privkey.io)

## Version

1.3.5
