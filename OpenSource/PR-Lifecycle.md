# The PR lifecycle

The practical path from "I want to contribute" to "merged" — the #1 blocker for new contributors. For a big repo like PyTorch/LLVM/ROCm the flow is: find work → claim → branch → code → CI → review → merge.

## Finding and claiming work

- **`good-first-issue` / `help-wanted` / `low-risk`** labels, and the project's contributor guide (every repo has one — read it; [[how-to-explore-large-repos]]).
- **Triage**: pick issues that are (a) real, (b) scoped, (c) have a clear acceptance test. Avoid "feature requests" needing design sign-off for your *first* PR.
- **Claim**: comment "I'll take this" / "I'd like to work on this" before spending effort — avoids duplicating a co-contributor; big projects track ownership on the issue.
- **WIP PRs**: opening a `Draft`/WIP PR *early* ("here's my direction, feedback welcome") is standard and valued — you get course-correction before writing 2000 lines. Follow the project's PR template.

## The branch and commit dance

- Branch from the project's `main`/`nightly` (not master), keep it **rebased** (not merged) onto upstream before merge-request.
- **Small PRs win**: one logical change per PR. Reviewers prefer 5 focused PRs to one monster (see [[writing-a-good-commit]] for commit message style; a clean commit *story* helps review).
- Sign-offs/CLA: many projects require `Signed-off-by` (DCO) or a Contributor License Agreement check — the CI will tell you.

## CI and the review loop

- **Run CI locally first** (the exact lint/format/test commands; big repos give a "quick checks" script). CI on the *project's* matrix is the real gate.
- **Review etiquette**: 
  - Address every comment (reply or fix); a `done` reply is fine.
  - Request review explicitly (`@maintainer`, or GitHub "re-request review") when it's ready — don't assume reviewers track your force-pushes.
  - **Never dismiss feedback** with silence. Maintainers gate their time on whether you respond.
- **Merge rituals**: `rebase -i`, squash if the project style wants it, mark "ready for review", wait for CI green, then the maintainer merges (often `pytorchmergebot`/`bors` style automation).

## The reality

- First PR may take *weeks* (review latency + CI queue). That's normal, not rejection.
- Reviewers look for: does it build/CI-green, tests included, docs updated (see the docs point in the PLAN), changelog entry. **Tests + docs are what make a PR mergeable**, not clever code.

## Related

- [[writing-a-good-commit]] — the commit half of the flow.
- [[code-review-skills]] — what reviewers are looking for.
- [[how-to-explore-large-repos]] — the pre-PR preparation.
- [[debugging-CI-failures]] — when your PR's CI goes red.
