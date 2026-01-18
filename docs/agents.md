# Agents

## code-execution

Execute code changes with mandatory verification.

**Triggers:** implement, fix, change, update, add feature, modify code

**What it does:**
1. Reads and understands the target code
2. Plans minimal changes required
3. Makes precise, targeted edits
4. Runs tests/build to verify
5. Confirms the change actually works

---

## pr-review

Deep audit PR changes and generate high-value review comments.

**What it does:**
1. Analyzes all changes in the PR branch
2. Audits for bugs, security issues, missing error handling
3. Generates comments with specific line numbers and severity

**Output format:**

> **File:** `src/auth.ts`
> **Line:** 42-45
> **Code:**
> ```ts
> if (user.isAdmin) {
>   grantAccess();
> }
> ```
> **Comment:** Missing null check on user object
> **Severity:** Blocker

---

## pr-fixes

Rebase PR on main and surgically fix issues for production.

**What it does:**
1. Rebases on main with signing (`git rebase -S`)
2. Deep audits all changes
3. Fixes issues surgically
4. Verifies tests/build pass

---

## refactor

Refactor code for simplicity and keep files under 500 lines.

**What it does:**
1. Finds files over 500 lines
2. Evaluates if splitting makes sense
3. Refactors for simplicity
4. Verifies nothing broke

---

## stress-test

Find crashes, memory issues, overflows, segfaults, and security vulnerabilities.

**What it does:**
1. Identifies attack surface (input handlers, memory ops, etc.)
2. Runs static analysis and sanitizers
3. Fuzzes inputs and tests edge cases
4. Tests for security vulnerabilities
5. Fixes issues surgically with verification

**Language-specific support:**
- **C**: Buffer overflow detection, sanitizers (ASan, UBSan), valgrind
- **Rust**: Unsafe code audit, Miri for UB detection, clippy lints

---

## security-review

Review code for security vulnerabilities including OWASP top 10.

**What it does:**
1. Identifies changed files in the branch
2. Scans for hardcoded secrets and credentials
3. Checks for injection vulnerabilities (SQL, command, path traversal)
4. Reviews auth/authorization patterns
5. Audits for XSS, cryptographic issues, memory safety

**Output:** Findings grouped by severity (Critical, High, Medium, Low) with file paths, line numbers, and fix recommendations.
