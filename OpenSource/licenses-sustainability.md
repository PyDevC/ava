# Open source licenses and sustainability

The legal + ecosystem layer of OSS. For contributors the two questions are: "what do the licenses mean when I contribute?" and "what keeps a project alive?" (See the PLAN's nice-to-have section.)

## Licenses at a glance

- **MIT**: do anything, keep the copyright notice. The "give it away freely" license — permissive, no copyleft. One line of attribution. The default choice for libraries that want maximal adoption (e.g. many ML tools).
- **Apache 2.0**: like MIT + explicit **patent grant** (important for companies — this is why Apache is the *default for DL frameworks*: PyTorch, TensorFlow, MLIR/LLVM use Apache or the BSD-family). Also has contribution terms: the "Contributor License Agreement" makes explicit what you're granting.
- **GPL**: copyleft — derivative works must also be GPL. Consumers *must* open-source their changes when distributing. Very common for Linux-kernel-ish infrastructure, rare in ML frameworks (kernel is GPL; drivers/libraries Apache). There's also LGPL (link-compatible for libraries), AGPL (also for network use).
- **BSD/Mozilla/CC**: variations — BSD ≈ MIT with wording differences; MPL is file-level copyleft (Firefox); CC licenses for docs/content.

## What it means when YOU contribute

- By sending a PR you're (usually implicitly) agreeing to the project's **DCO/CLA**: the code (and your right to publish it) is contributed under the project's license. GitHub's **DCO check** = "Signed-off-by" on your commits; **CLA** = a one-time agreement (Google/Amazon-style). Both are legally meaningful — don't paste copyrighted code into a PR.
- **Never contribute code you don't have the right to** (copy-pasted from a GPL project into an Apache project is a real legal problem, not a style nit).
- License of *your* project: pick based on goal (adoption → MIT/Apache; ecosystem obligation → GPL; dual-license for commercial → your call). The name and year belong in every file header your project's template wants.

## Sustainability — what keeps OSS alive

- **Maintainer burden**: triage, reviews, releases, security — 90% invisible work. Contributors who *also* review, triage, and document are the sustainers (see [[code-review-skills]]).
- **Security disclosures**: the `SECURITY.md` flow — private disclosure, coordinated fix window, CVE assignment, then public advisory. Never post a 0-day as a normal issue (see [[good-issue-reports]]).
- **Funding models**: corporate sponsorship, foundations (Linux Foundation, Apache Foundation), donations, dual-licensing. Understanding the model tells you how to get a *fix* accepted (a corporation-maintained component has a governance process, not just a repo).

## Related

- [[good-issue-reports]] — the security-disclosure etiquette.
- [[PR-Lifecycle]] — CLA/DCO are part of the flow.
- [[writing-a-good-commit]] — header/attribution style.
