# zeroclaw-simplify-review

ZeroClaw-specific simplify preflight for changes that are about to be committed, opened as a PR, or reviewed by maintainers. This is not a general replacement for `code-reviewer`; it is a narrower workflow for checking ZeroClaw architecture fit, known project pitfalls, and avoidable complexity.

```bash
zeroclaw skills install zeroclaw-simplify-review
```

## Permissions

- `file_read`: reads repository instructions, changed files, nearby source code, and tests so the review can be grounded in the actual ZeroClaw tree.

The skill is read-only by default. It does not require network access, shell execution, or file writes.

## Example usage

Ask an agent to run `zeroclaw-simplify-review` before opening a ZeroClaw PR, or on a PR diff that needs a focused reuse, project-fit, and simplicity pass before the normal review flow.
