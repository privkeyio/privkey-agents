---
description: "Run PR preparation pipeline: security-review, pr-fixes, then code-simplifier"
argument-hint: "[task description]"
---

# PR Pipeline Command

Execute a multi-stage pipeline to prepare code for PR merge.

## Pipeline Stages

1. **Stage 1**: `security-review` - Find security issues (read-only)
2. **Stage 2**: `pr-fixes` - Rebase on main, fix PR issues + security findings
3. **Stage 3**: `code-simplifier` - Clean up and simplify the code

## Instructions

Execute this pipeline using the Task tool. Run stages sequentially, waiting for each to complete.

### Stage 1: Security Review

```
Task: privkey-agents:security-review
- Find security issues (read-only)
- Save findings for Stage 2
```

### Stage 2: PR Fixes

Run pr-fixes normally, with security findings included:

```
Task: privkey-agents:pr-fixes
- Do full pr-fixes job: rebase on main, audit and fix all PR issues
- Additionally fix these security issues from Stage 1: [include findings]
- SKIP tests/build verification - will run once at the end
```

### Stage 3: Code Simplification

```
Task: code-simplifier:code-simplifier
- Simplify and refine the recently modified code
- SKIP tests/build verification - will run once at the end
```

### Stage 4: Test & Build

Run tests and build once, after all code changes are complete:

```bash
# Find and run tests (npm test, pytest, cargo test, go test, etc.)
# Find and run build (npm run build, cargo build, make, etc.)
```

Fix any failures before proceeding.

### Stage 5: Final Recap and Confirmation

After all stages complete, YOU (the orchestrator) must:

1. Run `git status` and `git diff` to see all uncommitted changes
2. Run `git log origin/main..HEAD --oneline` to see commits made during the pipeline
3. Provide a final recap:

```
## Pipeline Complete

### Changes Made
- List all fixes, security improvements, and simplifications

### Commits
- List commits added during this pipeline run

### Uncommitted Changes
- List any files with uncommitted changes

### Assessment
**High-value contribution?** Yes/No - Brief reasoning about the value this PR adds
**Ready to merge?** Yes/No
```

4. Ask the user: **"Ready to commit and push?"**

5. Only after user confirms:
   - Commit any uncommitted changes with a descriptive message
   - Push the branch

## User Task

$ARGUMENTS

## Execution

Begin Stage 1 now. Run the security-review agent.
