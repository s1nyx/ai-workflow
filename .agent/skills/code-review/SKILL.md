---
name: code-review
description: Use this skill to review code. It supports both local changes and remote Pull Requests
---

## Goal

Analyze a code and detect violations against the rules

## Rules

Every violation you report must be backed by verifiable proof. Choose exactly one form:
- **Citation** — a primary source (language spec, CVE, CWE, official framework/library docs, OWASP) with an exact URL and a verbatim excerpt (≤ 2 sentences) that directly justifies the violation. Blog posts, Stack Overflow, and AI-generated content are not acceptable.
- **Test** — a minimal, self-contained snippet that reproduces the defect and produces an observable failure (crash, wrong output, data leak, perf regression…), with the expected vs actual behavior stated inline.

If neither form can be provided with confidence, the violation MUST be omitted entirely.


### Correctness
- Off-by-one errors, wrong loop bounds, incorrect conditions
- Null/undefined dereferences, missing nil checks
- Race conditions, concurrency issues (shared state, missing locks)
- Wrong types, implicit coercions, precision loss (float vs int)
- Incorrect algorithm logic or edge case handling

### Security
- Injection vulnerabilities (SQL, command, LDAP, XPath...)
- XSS, CSRF, open redirects
- Sensitive data exposed in logs, errors, URLs, or client responses
- Hardcoded secrets, tokens, credentials, or environment-specific values
- Broken auth: missing authentication/authorization checks, privilege escalation
- Insecure deserialization, path traversal, improper input validation

### Error Handling
- Unhandled exceptions, swallowed errors (empty catch blocks)
- Missing error handling on I/O, network calls, DB queries, external APIs
- Errors returned but not checked by the caller
- No fallback or recovery logic for expected failure modes
- Misleading or missing error messages surfaced to users or logs

### Performance
- Algorithmic inefficiency — flag Big-O complexity with justification (e.g. O(n²) vs O(n log n))
- N+1 queries, missing DB indexes, unbounded result sets
- Unnecessary re-computation inside loops, redundant network/disk calls
- Missing caching for expensive or repeated operations
- Large payloads, missing pagination, unstreamed large datasets
- Blocking calls on hot paths or event loops

### Reliability
- Missing retries or timeouts on network/DB calls
- No circuit breaker or graceful degradation for external dependencies
- Resources not released (open files, DB connections, sockets, timers)
- Operations that are not idempotent but should be
- Logic that silently fails without observable signal (no log, no metric, no error)

### Code Quality
- Duplicated logic that should be extracted or shared
- Functions/methods doing too many things (violating SRP)
- Deeply nested control flow that could be flattened or extracted
- Magic numbers or strings with no named constant or explanation
- Dead code, unused variables, unreachable branches
- Inconsistent naming (misleading, abbreviated, or ambiguous identifiers)

### Developer Experience
- Missing or outdated inline comments on non-obvious logic
- Public API (functions, classes, modules) without documentation
- Test coverage gaps on critical paths or edge cases
- Flaky or non-deterministic test logic
- Overly complex abstractions that reduce readability without clear benefit

### User Experience
- Error messages that are cryptic, technical, or unhelpful to end users
- Missing loading states, empty states, or feedback on async operations
- Inconsistent behavior across similar actions or flows
- Accessibility issues (missing ARIA attributes, keyboard navigation, contrast)
- Operations with no undo, confirmation, or warning for destructive actions

## Output format

Return only a JSON block - no commentary before or after

### Field constraints

Ensure the JSON adheres strictly to these rules:
- `path` (string)
- `lines` (string) (regex: `^\d+(-\d+)?$`)
- `severity` (string) (enum: ["error", "warning", "info"])
- `rule` (string) 
- `message` (string)

### Template

```json
{
  "violations": [
    {
        "path": "src/example.ts",
        "lines": "12-14",
        "severity": "error",
        "rule": "Performance issues",
        "message": "The function `foo` has a time complexity of O(n^2) which is inefficient for large inputs"
    }
  ]
}
```
