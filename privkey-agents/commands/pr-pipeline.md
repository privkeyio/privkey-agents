---
description: "Run PR preparation pipeline: security-review, pr-review, pr-fixes, verification, then code-simplifier"
argument-hint: "[task description]"
---

# PR Pipeline Command

Execute a multi-stage pipeline to prepare code for PR merge.

## Pipeline Stages

1. **Stage 1**: `security-review` + `pr-review` - Find all issues (read-only, parallel)
2. **Stage 2**: `pr-fixes` - Rebase on main, fix ALL findings from stage 1
3. **Stage 3**: `pr-review` - Re-audit changes made by pr-fixes (catch introduced bugs)
4. **Stage 4**: `code-simplifier` - Clean up and simplify the code
5. **Stage 5**: Test & Build

## Instructions

Execute this pipeline using the Task tool.

### Stage 1: Security + Code Quality Review (Parallel)

Run BOTH agents in parallel (single message with multiple Task calls):

```
Task: privkey-agents:security-review
- Find security issues (read-only)

Task: privkey-agents:pr-review
- Find memory leaks, logic bugs, consistency issues (read-only)
```

Wait for both to complete, then combine findings for Stage 2.

### Stage 2: PR Fixes

```
Task: privkey-agents:pr-fixes
- Do full pr-fixes job: rebase on main, audit and fix all PR issues
- Fix these security issues from Stage 1: [include security-review findings]
- Fix these code quality issues from Stage 1: [include pr-review findings]
- SKIP tests/build verification - will run once at the end
```

### Stage 3: Verify Changes (Critical)

Re-run pr-review on ONLY the files modified by Stage 2:

```bash
# Get files changed by pr-fixes
git diff --name-only HEAD~1
```

```
Task: privkey-agents:pr-review
- Review ONLY files from the list above
- Focus on: Did pr-fixes introduce new issues?
- Check: consistency of new constants, error handling, state cleanup
```

If Stage 3 finds issues, fix them immediately (no new agent needed).

### Stage 4: Code Simplification

```
Task: code-simplifier:code-simplifier
- Simplify and refine the recently modified code
- SKIP tests/build verification - will run once at the end
```

### Stage 5: Test & Build

Run tests and build once, after all code changes are complete:

```bash
# Find and run tests (npm test, pytest, cargo test, go test, etc.)
# Find and run build (npm run build, cargo build, make, etc.)
```

Fix any failures before proceeding.

### Stage 6: Final Recap and Confirmation

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
