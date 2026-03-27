---
name: code-review
description: Use this skill to review code. It supports both local changes and remote Pull Requests
---

## Goal

Analyze a code and detect violations against the rules

## Rules

Check for at least:
- Performance issues: the code must be ultra-efficient (in cpu, disk, network, memory, ...), code speed (big O notation)
- Obvious bugs (off-by-one errors, null pointer dereferences, ...)
- Security issues (SQL injection, XSS, ...)
- Missing error handling (I/O, network, DB call, ...)
- Hardcoded values
- Code quality
- Developer Experience
- User Experience

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
    "path": "src/example.ts",
    "lines": "12-14",
    "severity": "error",
    "rule": "Performance issues",
    "message": "The function `foo` has a time complexity of O(n^2) which is inefficient for large inputs"
  ]
}
```