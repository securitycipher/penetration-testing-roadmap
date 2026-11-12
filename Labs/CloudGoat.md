# CloudGoat

CloudGoat is Rhino Security Labs' "vulnerable by design" **AWS** training tool. It uses Terraform to deploy deliberately insecure scenarios into *your own* AWS account, each with a starting foothold and a goal (reach admin, read a secret, pivot to another service). It is the best hands-on way to learn cloud attack paths and to practice with [Pacu](../Cloud/Pacu.md).

## Important: it costs real money

CloudGoat deploys real AWS resources in your account. Keep scenarios short and **always `destroy`** when done to avoid charges. Use a dedicated test account, never a production one.

## Setup

```bash
git clone https://github.com/RhinoSecurityLabs/cloudgoat
cd cloudgoat && pip install -r requirements.txt
./cloudgoat.py config profile          # point at your AWS CLI profile

# Deploy a scenario
./cloudgoat.py create iam_privesc_by_rotation

# ... attack it ...

# Tear it down (do not skip this)
./cloudgoat.py destroy iam_privesc_by_rotation
```

## What you practice

- **IAM privilege escalation** (the many known AWS privesc paths)
- **SSRF to IMDS** credential theft (see [SSRF](../OWASP%20Top%2010/SSRF.md))
- Lambda, EC2, S3, and RDS misconfigurations
- Pivoting across services with stolen credentials

## How it fits

1. Deploy a scenario and read its starting credentials/goal.
2. Enumerate with [Pacu](../Cloud/Pacu.md) and the AWS CLI.
3. Find the privesc or misconfig path to the goal.
4. `destroy` the scenario.

## Resources

- [CloudGoat on GitHub](https://github.com/RhinoSecurityLabs/cloudgoat)
- [Pacu](../Cloud/Pacu.md), [AWS](../Cloud/AWS.md)
- Free AWS-focused alternative: **flAWS** / **flAWS2** (`flaws.cloud`)
