# Prowler

Prowler is an open-source cloud security tool for AWS, Azure, GCP, and Kubernetes. It replaced much of what teams used CloudSploit for and adds CIS benchmark checks.

## What it checks

- IAM misconfigurations (overly permissive policies, root key usage)
- Public S3 buckets and EBS snapshots
- Security group rules (0.0.0.0/0 on sensitive ports)
- CloudTrail and logging gaps
- Kubernetes RBAC and API exposure

## Quick start (AWS)

```bash
# Install
pip install prowler

# Run full AWS assessment
prowler aws

# Specific check
prowler aws --checks s3_bucket_public_access

# Output HTML report
prowler aws -M html
```

## vs ScoutSuite vs CloudSploit

| Tool | Status (2026) | Best for |
|------|---------------|----------|
| Prowler | Actively maintained | AWS/Azure/GCP/K8s CIS checks |
| ScoutSuite | Maintained | Multi-cloud visual reports |
| CloudSploit (Aqua) | Legacy name | Use Prowler or Trivy instead |

## Resources

- [Prowler GitHub](https://github.com/prowler-cloud/prowler)
- [AWS Cloud Security Checklist](https://securitycipher.com/aws-cloud-security-checklist/)

## Related

- [ScoutSuite](ScoutSuite.md)
- [Trivy](Trivy.md)
