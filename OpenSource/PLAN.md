# OpenSource — Plan

## Goal of this directory

Capture **how to work in open source**: navigating large codebases, making good contributions (commits, PRs, reviews), and understanding the contribution cycle. The goal is to be a productive contributor to big projects like PyTorch, LLVM/MLIR, ROCm, Kubernetes.

## What's already here

- [[how-to-explore-large-repos]] — using the software, reading docs/wikis/contribution guides, starting small, the practical workflow for big repos.
- [[writing-a-good-commit]] — commit message guidelines and a comprehensive commit-type reference.
- [[PR-Lifecycle]] — finding/claiming work, WIP PRs, branch/CI/review etiquette, merge.
- [[code-review-skills]] — what reviewers look for, reviewing others' PRs.
- [[debugging-CI-failures]] — reading CI logs, test matrices, local-first debugging.
- [[good-issue-reports]] — repro-first bug reports, feature/RFC vs security channels.
- [[licenses-sustainability]] — MIT/Apache/GPL, DCO/CLA, maintainer burden, security disclosures.

## What to add next

- [x] **The contribution lifecycle**:
  - [x] Finding issues (good-first-issue, triaging), claiming work, WIP PRs
  - [x] PR workflow: branches, CI, review etiquette, addressing feedback, when to request review
  - [x] Code review skills: what reviewers look for, reviewing others' PRs
- [x] **Working with big-project CI** (extends the repo-exploration note):
  - [x] Reading CI logs, debugging a failing check (see [[PyTorch/CI_Infra]] for a real example)
  - [x] Understanding the test matrix (which CI job tests what)
- [x] **Communication**:
  - [x] Writing good issue reports / bug reports (repro, environment, expected vs actual)
  - [x] RFC/discussion etiquette; reading design docs (PyTorch RFCs, LLVM discourse)
  - [x] Update docs along with code (maintainers love this — ties to [[writing-a-good-commit]])
- [x] **Sustainability / licensing** (nice-to-have):
  - [x] Open source licenses at a glance (MIT/Apache/GPL) — what they mean for contributions
  - [x] Maintainer responsibilities, security disclosures, CVE etiquette

## Prioritization

1. **PR lifecycle + review etiquette** — the practical blocker for new contributors.
2. **Good issue reports** — the lowest-effort, high-value contribution skill.
3. **CI debugging** — unblocks me when a real PR fails.
