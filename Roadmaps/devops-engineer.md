# DevOps Engineer

DevOps engineers build and maintain the **infrastructure that software (and ML) runs on**: CI/CD pipelines, container orchestration, cloud, monitoring, and reliability. For ML orgs this shades into MLOps.

## Core skills

- **Linux + shell** fundamentals, system administration.
- **Version control** (git), **CI/CD** (GitHub Actions, GitLab CI, Jenkins) — see [CI_Infra](../PyTorch/CI_Infra.md) for how a big project does this.
- **Containers & orchestration**: Docker, Kubernetes (pods, services, Helm), container registries.
- **Cloud**: AWS/GCP/Azure — VMs, storage, IAM, networking. **Infra-as-code**: Terraform, Ansible.
- **Observability**: logging, metrics, alerting (Prometheus, Grafana, Datadog).
- **Scripting/automation**: Python, bash; some Go/JS for tooling.
- **Security basics**: secrets management, least-privilege IAM, image scanning.

## Typical day-to-day

- Automating builds and deploys; managing k8s clusters; scaling services.
- On-call: fixing broken pipelines and flaky infra.
- For ML: GPU cluster management, model serving infrastructure (see [ml-engineer](ml-engineer.md) for the ML side).

## Roadmap to get there

1. Linux, bash, networking basics, git.
2. One cloud (AWS most common): compute, storage, IAM.
3. Docker → Kubernetes (that's the big jump; learn via minikube).
4. CI/CD with GitHub Actions; infra-as-code with Terraform.
5. Observability stack; then specialize: platform/SRE, or MLOps.

## Related

- [hpc-engineer](hpc-engineer.md) — higher-performance, research-grade infra cousin.
- [ml-engineer](ml-engineer.md) — ML's deployment needs land on DevOps skills.
- [CI_Infra](../PyTorch/CI_Infra.md) — real-world example of CI infrastructure at scale.
