# How to explore large repositories

Contributing to really large projects (PyTorch, TensorFlow, Kubernetes, ROCm, Linux) is a months-long ramp-up, but the process is learnable. The goal isn't to read everything — it's to build enough mental model to make your first useful contribution.

## Get familiar before you dig in

1. **Use the thing first.** If it's a library, build small projects with it; if it's a tool, use it day-to-day. Using gives you the "what happens" layer that makes the code make sense. Work on projects you already care about or use — that keeps you motivated and gives you real bugs to hit.
2. **Read the contribution guidelines** (CONTRIBUTING.md, the repo wiki, docs). Following them signals you take the maintainers' workflow seriously — that's most of the first-impression game in OSS.
3. **Find the entry-point docs**: wiki, docs site, or `docs/` dir. That's the fast path to the architecture before you touch code.
4. **Use `git log`/`git blame`** to see how the code evolved and who owns it — history is often the best documentation of *why*.

## Don't read everything

In a big project you can't know all parts — and shouldn't. The codebase changes constantly, so staying fully current is a losing game. Start small and grow outward:

- Pick one subsystem or one feature area and understand it end-to-end.
- Follow one code path (e.g. in PyTorch: a tensor op from Python → dispatch → kernel — see [PyTorch/Explore](../PyTorch/Explore.md) and [how-to-explore-large-repos](../OpenSource/how-to-explore-large-repos.md)).
- Let issues and PRs you care about pull you into the parts you need.

Working this way means you never drown in the file tree trying to reverse-engineer "why it's programmed that way" — you learn what you need, when you need it.

## Related

- [PyTorch/Explore](../PyTorch/Explore.md) — a concrete map of a huge repo.
- [good-issue-reports](good-issue-reports.md), [PR-Lifecycle](PR-Lifecycle.md) — the contribution cycle once you're in.
