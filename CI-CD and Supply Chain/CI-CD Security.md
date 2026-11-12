# CI/CD Security

Continuous Integration / Continuous Deployment (CI/CD) pipelines build, test, and ship code automatically. They are high-value targets because they hold powerful credentials (cloud keys, registry tokens, signing keys) and can push code straight to production. Compromise the pipeline and you often get everything downstream. This is the attacker's side of OWASP's **Software Supply Chain Failures** (new in the 2025 Top 10).

## Why pipelines are juicy

- They store secrets for cloud, registries, and deploy targets
- They run attacker-influenced input (PRs, dependencies, build scripts)
- A single pipeline often deploys to many environments
- Developers trust pipeline output implicitly

## Common attack paths

- **Poisoned Pipeline Execution (PPE)** - inject commands via a modifiable build file (`Makefile`, `package.json` scripts, pipeline YAML) so your code runs in CI.
- **Malicious pull requests** - a PR from a fork triggers a workflow that runs untrusted code with secrets in scope (see [GitHub Actions Security](GitHub%20Actions%20Security.md)).
- **Dependency attacks** - [dependency confusion](Dependency%20Confusion.md), typosquatting, and compromised packages pull malicious code into the build.
- **Secret theft** - dump environment variables and tokens from a build; find secrets in logs and artifacts.
- **Artifact / registry tampering** - push a backdoored image or package others trust.
- **Insufficient isolation** - a build job can reach production or other tenants.

## How to test a pipeline

1. Map the pipeline: triggers, who can modify build files, what secrets are present, where it deploys.
2. Can an external contributor's PR run privileged workflows with secrets?
3. Try PPE: modify a build/test script and see if it executes with credentials.
4. Check secret handling: are they masked in logs? reachable by any job?
5. Verify artifact signing and whether consumers verify signatures.

## Mitigation

- Least-privilege, **short-lived** pipeline credentials (OIDC to the cloud instead of long-lived keys)
- Isolate and sandbox build jobs; never run untrusted PRs with secrets
- Require review/approval for workflow and build-file changes
- Pin dependencies and actions by hash; use a private registry with priority
- **Sign artifacts** (Sigstore/cosign) and verify on consumption; generate an SBOM (see [Trivy](../Cloud/Trivy.md))
- Follow SLSA levels for build integrity

## Related

- [GitHub Actions Security](GitHub%20Actions%20Security.md), [Dependency Confusion](Dependency%20Confusion.md)
- [Software and Data Integrity Failures](../OWASP%20Top%2010/Software%20and%20Data%20Integrity%20Failures.md)
