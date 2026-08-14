# Code review skills

Being a good reviewer is the fastest path to becoming a better contributor — and it's half of what makes big OSS sustainable. This is what maintainers actually look for (see [[PR-Lifecycle]] for the contributor side).

## Reviewing others' PRs

- **Start with intent**: what problem does the PR solve, and does the approach fit the codebase's established patterns (a PR that reinvents the wheel gets sent back regardless of correctness)?
- **Read in order**: tests → interface → implementation. If the tests don't demonstrate the change, that's the first comment.
- **Comment on the code, not the person**: objective, specific, actionable ("this returns wrong values when `n==0` — add a test" vs "this is bad"). This is the social contract of every OSS community.
- **The four buckets**: correctness bugs, missing tests, style/consistency, docs. Distinguish "must fix" from "nit" explicitly (`nit:` prefix is universal).

## What reviewers look for (the checklist)

- **Correctness**: edge cases, error handling, resource/lifetime correctness (esp. in C++: ownership, exceptions, see [[Programming/PLAN]]), thread-safety claims.
- **Tests**: do they cover the change? Regression tests for the *fixed bug*, not just the happy path?
- **Public API**: is the new API well-named, documented, and backward-compatible? (For PyTorch: does it need a `torch.library`/dispatch registration, see [[PyTorch/Custom-Ops]].)
- **Performance**: unnecessary copies (C++: passing by value when `const&` is right), hot-path allocations, hidden O(n²).
- **Scope**: does the PR do one thing? (The single biggest review complaint.)
- **Docs**: changelog/release-note entries, docstrings, migration notes (ties to the "docs along with code" rule).

## The review interaction model

- **Approve vs request changes** honestly; `Approved` is a strong signal.
- **Automation helps**: CI does lint/tests (see [[debugging-CI-failures]]); a human review focuses on *design and correctness*, not formatting.
- **LGTM ≠ rubber stamp**: the project's "commit to the fast path" culture. A reviewer who reads carefully earns trust; a drive-by "LGTM" loses it.
- **Respect the project's process**: some repos require two approvals, some require a specific maintainer, some merge via bot (`bors`/`pytorchmergebot`) — follow the CONTRIBUTING.md gate.

## The skill that compounds

Reviewing others' code teaches the codebase's idioms far faster than writing new code — you see the patterns reviewers reward. Do it early, on small PRs, with honest rigor. The people whose PRs you review will review yours.

## Related

- [[PR-Lifecycle]] — the flow review is a stage of.
- [[writing-a-good-commit]] — what a reviewable change looks like.
- [[debugging-CI-failures]] — the automated checks reviewers rely on.
