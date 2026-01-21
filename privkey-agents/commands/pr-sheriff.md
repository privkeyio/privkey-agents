---
description: "PR Sheriff standing orders - review incoming PRs, merge easy wins, flag others for review"
argument-hint: "[rig name]"
---

# PR Sheriff

Standing orders for continuous PR triage. Run this on a crew member to make them a permanent PR Sheriff.

## Standing Orders

On every session startup:

### 1. Check for Beads

```bash
bd version
```

If CLI is installed, check if repo is initialized:
```bash
test -d .beads || bd init --stealth
```

### 2. Scan Open PRs

```bash
gh pr list --state open --json number,title,author,additions,deletions,changedFiles
```

### 3. Triage Each PR

**Easy wins** (auto-merge candidates):
- < 100 lines changed
- Single file or config-only changes
- From trusted contributors
- Passing CI

**Needs review**:
- > 100 lines changed
- Security-sensitive files
- New dependencies
- Failing CI

### 4. Process Easy Wins

For each easy win:
1. Run quick `pr-review` to verify no blockers
2. If clean, merge: `gh pr merge <number> --squash`
3. File bead: `bd create -t "Merged PR #X: [title]" -d "Auto-merged by PR Sheriff"`

### 5. Flag for Human Review

For PRs needing review:
```bash
bd create -t "Review PR #X: [title]" -d "[reason it needs human review]" -p high
```

### 6. Report Summary

Output a summary:
```
## PR Sheriff Report

### Merged (Easy Wins)
- PR #123: Fix typo in README
- PR #124: Update dependencies

### Flagged for Review
- PR #125: New authentication flow (200+ lines, security-sensitive)
- PR #126: Database migration (needs human verification)

### Skipped
- PR #127: Draft PR
```

## Usage

Tell a crew member:
```
"You are the PR Sheriff for this rig. Pin this task and run it on every session."
```

Or sling it:
```bash
gt sling pr-sheriff <rig>
```

## Notes

- This is a standing order - the sheriff runs this on every session
- Pin the task so it survives handoffs: `bd update <id> --pin`
- The sheriff should `gt handoff` after completing the sweep
