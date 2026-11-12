# Pacu

Pacu is an open-source **AWS exploitation framework** from Rhino Security Labs - think Metasploit for AWS. Once you have a set of AWS credentials (access key + secret), Pacu helps you enumerate what they can do, escalate privileges, find misconfigurations, and pivot through the account. It is the offensive counterpart to defensive scanners like [Prowler](Prowler.md) and [ScoutSuite](ScoutSuite.md).

## Install

```bash
pipx install pacu
pacu                       # starts the interactive console
```

## Workflow

1. Set up credentials you obtained (from a leaked key, SSRF to IMDS, a compromised host):

```text
set_keys                   # paste access key id + secret (+ session token)
```

2. Enumerate what the identity can see and do:

```text
run iam__enum_permissions
run iam__enum_users_roles_policies_groups
run ec2__enum
run s3__download_bucket
```

3. Look for privilege escalation paths:

```text
run iam__privesc_scan      # finds known IAM privesc paths
```

## What it is good at

- Mapping effective permissions of a given key
- Finding IAM privilege-escalation chains (there are many known ones)
- Enumerating EC2, S3, Lambda, RDS, and more
- Backdooring, persistence, and log-tampering modules (use only in scope)

## How you get the initial credentials

- Leaked keys in Git history / S3 / client-side code
- SSRF to the EC2 metadata endpoint (IMDSv1) - see [SSRF](../OWASP%20Top%2010/SSRF.md)
- Compromised host with an instance profile
- Over-permissive public resources

## Mitigation (for the report)

- Enforce least-privilege IAM; avoid wildcard `*` actions/resources.
- Require **IMDSv2** to blunt SSRF credential theft.
- Rotate and scan for leaked keys; use short-lived role credentials.
- Enable CloudTrail and alert on enumeration/privesc patterns.

## Practice

- [CloudGoat](../Labs/CloudGoat.md) - vulnerable-by-design AWS scenarios built for this

## Resources

- [Pacu on GitHub](https://github.com/RhinoSecurityLabs/pacu)
- [AWS](AWS.md), [AWS Cloud Security Checklist](https://securitycipher.com/aws-cloud-security-checklist/)
