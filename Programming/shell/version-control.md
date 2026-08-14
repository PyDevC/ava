# Version control beyond basics

`git add`/`commit`/`push` gets you through solo work. The tools here are what make git genuinely powerful: rewriting history safely, debugging with the commit graph, and working on many things at once. All of this matters for OSS work (see [PR-Lifecycle](../../OpenSource/PR-Lifecycle.md) and [writing-a-good-commit](../../OpenSource/writing-a-good-commit.md)).

## Interactive rebase — history surgery

- `git rebase -i HEAD~3` opens an editor listing the last 3 commits. Actions per line: `pick` (keep), `reword` (edit message), `edit` (stop to amend files), `squash`/`fixup` (fold into previous; `fixup` drops the message), `drop`, `reorder` (just move lines).
- This is how I turn "three messy commits" into one clean one before a PR — the exact thing reviewers reward (see [writing-a-good-commit](../../OpenSource/writing-a-good-commit.md)).
- **Rebase vs merge**: rebasing rewrites your branch onto a newer base (linear history, clean PRs); merging keeps the merge commits. For PR-based OSS, rebase onto trunk, don't merge trunk in.
- Danger: rebasing *published* commits rewrites history others share — `git push --force` only for branches only you own, and prefer `--force-with-lease`.

## git bisect — the automated bug hunt

- `git bisect start`, `git bisect bad` (current = broken), `git bisect good <commit>` (last known-good), then binary search: it checks out a middle commit; `git bisect good/bad` per step until it names the commit that introduced the bug.
- **The automation trick**: `git bisect run <script>` — the script exits 0 (good) or non-zero (bad). For build/test failures the script is usually `build && run the test`; half an hour of bisecting becomes minutes of one command. This is the canonical way to find a regression (see [good-issue-reports](../../OpenSource/good-issue-reports.md) for the report side).

## Worktrees — multiple checkouts

- `git worktree add ../mb-fix -b fix/branch` checks out a *new branch in a separate directory* from the same repo. No stashing to switch context, no clobbered build dirs.
- Perfect for the "I'm mid-PR on main branch, need to repro an old tag" problem — add a worktree at the tag, leave your working tree untouched.

## Submodules — pinned external deps

- `git submodule add <url> path`, `git submodule update --init --recursive` to clone. A submodule pins an external repo to a *commit*, not a branch.
- The pain: they break CI and confuse everyone, and a `git pull` doesn't update them. If you don't need the pinned commit, don't add one — vendoring or a package manager is usually cleaner.

## reflog — the undo of last resort

- `git reflog` lists every HEAD movement (including resets, rebases, checkouts), even ones "gone". If I `git reset --hard` the wrong thing, the old commits are in the reflog and recoverable.
- Reflog entries expire (default ~90 days), but that's the difference between "lost" and "recoverable" — try reflog before panicking.

## stash, advanced

- `git stash push -m "wip"` with `-u` (untracked) or `-p` (partial). `git stash pop` restores; `git stash apply <n>` keeps it.
- The danger is stash *conflicts* on pop and forgetting what's stashed. Modern default: use a branch or worktree for real work-in-progress; stash only for "quick context switch".

## Fixing mistakes: reset vs revert

- **`git revert <commit>`**: adds a new commit undoing the change — *published* history stays valid, safe for shared branches. Always the answer for a PR/trunk mistake.
- **`git reset`**: moves HEAD back, rewriting history — for *unpublished* local mistakes only. `--soft` keeps changes staged, `--mixed` keeps them unstaged, `--hard` discards.
- The rule that ends the debate: **shared history → revert; local history → reset.**

## Why it matters

Bisect finds regressions in minutes that a manual hunt would take an afternoon; interactive rebase is what keeps PRs reviewable; worktrees remove the "I can't, I'm on the wrong branch" excuse. These are the skills that make large-repo contribution practical (see [how-to-explore-large-repos](../../OpenSource/how-to-explore-large-repos.md)).

## Related

- [bash-scripting](bash-scripting.md) — the shell I run git from, `set -euo pipefail` for bisect scripts.
- [PR-Lifecycle](../../OpenSource/PR-Lifecycle.md) — the workflow rebase/revert plug into.
- [writing-a-good-commit](../../OpenSource/writing-a-good-commit.md) — what to leave in the history after rebasing.
- [PLAN](../) — the Programming area this lives in.
