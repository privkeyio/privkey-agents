---
description: "Run PR preparation pipeline: pr-fixes, security-review, code-execution, then code-simplifier"
argument-hint: "[task description]"
---

# PR Pipeline Command

Execute a multi-stage pipeline to prepare code for PR merge.

## Pipeline Stages

1. **Stage 1**: `pr-fixes` - Rebase on main, audit and fix PR issues
2. **Stage 2**: `security-review` - Security audit of the fixed code
3. **Stage 3**: `code-execution` - Fix any security issues identified
4. **Stage 4**: `code-simplifier` - Clean up and simplify the code

## Instructions

Execute this pipeline using the Task tool. Run stages sequentially, waiting for each to complete.

### Stage 1: PR Fixes

```
Task: privkey-agents:pr-fixes
- Rebase on main and fix PR issues
```

### Stage 2: Security Review

After pr-fixes completes:

```
Task: privkey-agents:security-review
- Security audit of the fixed code
```

### Stage 3: Code Execution

If security review found issues:

```
Task: privkey-agents:code-execution
- Fix the security issues identified
- Prompt should include the specific issues found
```

If no security issues, skip to Stage 4.

### Stage 4: Code Simplification

```
Task: code-simplifier:code-simplifier
- Simplify and refine the recently modified code
```

## User Task

$ARGUMENTS

## Execution

Begin Stage 1 now. Run the pr-fixes agent.
