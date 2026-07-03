# Trivy

Trivy is Aqua Security's all-in-one scanner for container images, filesystems, Git repos, and IaC (Terraform, CloudFormation).

## Use cases

- Scan Docker images before deploy
- Find secrets in Git history
- Check Terraform for misconfigs
- SBOM generation for supply chain audits

## Quick commands

```bash
# Scan container image
trivy image nginx:latest

# Scan local filesystem
trivy fs .

# Scan git repo for secrets
trivy repo https://github.com/org/project

# IaC scan
trivy config ./terraform/
```

## Why it matters for pentesters

- Supply chain attacks often start with a vulnerable dependency
- Container escapes pair with outdated runc/kernel CVEs
- Complements [Docker Security](../Containers/Docker%20Security.md) testing

## Resources

- [Trivy GitHub](https://github.com/aquasecurity/trivy)
