# Writing good issue reports

The lowest-effort, highest-value contribution skill: a well-written bug report is a PR waiting to happen. Maintainers triage dozens of reports; a repro that runs in 5 seconds gets fixed, a wall of text without repro gets closed.

## The anatomy of a report that gets fixed

1. **Title = symptom + scope**: "CUDA: `torch.add` gives wrong result on shape (2,0)" beats "bug in torch". Include the component (from the issue template's labels) and environment.
2. **Minimal reproduction** — the *entire* point:
   - A small, self-contained script (`import` → trigger → assert). No company data, no notebook.
   - Run it on the *latest* release/nightly first (half the reports are already-fixed).
   - Pin versions in the script (`torch==2.x+cu121` etc.).
3. **Expected vs actual**: exactly what you expected, exactly what happened (paste the full traceback — the *first* error line is gold, see [debugging-CI-failures](debugging-CI-failures.md)).
4. **Environment**: OS, hardware, CUDA/ROCm version, Python version, install method (pip/conda/build-from-source). The project's template usually asks for exactly this — fill it.
5. **Search first**: check open+closed issues for the same symptom and *link* it ("duplicate of #1234, but with a repro"). Triagers love this.

## Bug-report anti-patterns

- "It doesn't work" without repro or logs.
- Pasting a 500-line traceback from a giant framework crash — *minimize* to the first error (see the minimize step in [debugging-CI-failures](debugging-CI-failures.md)).
- Reporting a crash without version info, or a *known* issue ("the bug is in <unrelated component>").
- Forgetting you can self-triage: if your repro is solid, it's often faster to *fix it yourself* as a PR (see [PR-Lifecycle](PR-Lifecycle.md)).

## Feature requests / RFCs — different beast

- Feature requests go through the project's design channel (PyTorch RFCs, LLVM discourse) — post the *motivation* and the *acceptance criteria*, not just "please add X". Design discussion is a conversation, not a ticket (see the PLAN's RFC-etiquette item).
- Security issues: do **NOT** post a public report — use the project's security contact / `SECURITY.md` disclosure process (private), see the licensing/sustainability note.

## The template that works

```
**Description**: 2 lines
**Repro**: 5-line script
**Expected**: ...
**Actual**: <first error line + traceback>
**Env**: OS / HW / torch-cuda versions / install method
**Versions**: tried latest (commit/nightly), still broken
```

## Related

- [debugging-CI-failures](debugging-CI-failures.md) — reading logs feeds the report.
- [PR-Lifecycle](PR-Lifecycle.md) — turning the report into a PR.
- [code-review-skills](code-review-skills.md) — maintainers judge your report the way they judge code.
