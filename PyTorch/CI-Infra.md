# CI Infrastructure for PyTorch

PyTorch uses both Github Hosted Linux containers as well as AWS Linux Runners, Linux Foundation Runners, Google Linux XPU runners, AMD Runners, etc. see actionlint.yaml file for more runners.

## Who provides the runners

The CI is multi-cloud and community managed. Runner pools come from several places:

- GitHub-hosted runners (the basic `ubuntu-latest` style ones) for lightweight jobs
- AWS Linux runners - the main self-hosted fleet, managed by the PyTorch Foundation's cloud accounts
- Linux Foundation runners (hosted on the LF's OSDC, spanning `x86_64`, `aarch64`, and `ppc64le`)
- Google Linux XPU runners - for Intel/Google accelerator (XPU) testing
- AMD runners - for ROCm based GPU jobs
- plus ecosystem runners contributed by member companies (NVIDIA, ARM, Huawei, IBM, etc.)

## Actionlint

The GitHub Actions workflow files are linted with `actionlint` (see the `actionlint.yaml` / CI workflow files in the repo). Actionlint validates the workflow schema deeply - it type-checks expressions, validates action inputs, catches runner label typos and detects untrusted `${{ github.* }}` values being interpolated into shell scripts. That last one is important because CI runs untrusted PR content, and interpolating things like `github.event.pull_request.title` directly into `run:` steps is an injection vector.

## What to know about the CI

- The whole thing is orchestrated through GitHub Actions workflows in `.github/workflows/` plus scripts under `.ci/`
- There is a huge matrix of jobs: build, test (CPU/GPU/MPS/XPU), lint, docs, and the compile-only suites for the compilers (TorchDynamo / TorchInductor) and the many backends
- A lot of the infrastructure is managed with an autoscaler that spins up runners on demand (currently mostly in AWS); work is ongoing to make it multi-cloud so jobs can shift to other providers
- `@pytorchbot` handles merges/reverts, keeping trunk green; the "trunk" is the default branch that must always pass
- If you change the workflows, run actionlint locally (`actionlint` in the repo root) before pushing, otherwise CI will flag it

## Resources

- <https://github.com/pytorch/ci-infra> - infra repo with runner configs
- PyTorch Foundation TAC / CI working group meeting notes and slides (linked from pytorch.org blog)
