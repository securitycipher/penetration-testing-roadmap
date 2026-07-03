# What is Serverless?

Serverless computing lets you run code without provisioning or managing servers. You write functions, upload them, and the cloud provider spins up a short-lived container to run each one on demand. You pay only for execution time, which makes it cheap for spiky or event-driven workloads. AWS Lambda, Azure Functions, and Google Cloud Functions are the big three.

## How it works

- You deploy a function instead of a whole server
- An event (HTTP request, file upload, queue message, cron) triggers it
- The provider runs it in an ephemeral container, then tears it down
- Billing is per invocation and per millisecond of runtime

## Key benefits

- **Scales automatically** - no auto-scaling config to babysit
- **Cost-efficient** - pay for execution, not idle capacity
- **Less ops** - no OS patching or server management
- **Faster shipping** - focus on code, not infrastructure

## Security risks to test

- **Over-privileged execution roles** - a function's IAM role often has far more access than it needs
- **Event-data injection** - triggers come from many sources (S3, SQS, API Gateway), each an untrusted input path
- **Secrets in env vars** - readable by anyone who can view the function config
- **Vulnerable dependencies** - fat deployment packages ship old libraries
- **Broken function URLs / API Gateway auth** - publicly invokable functions

## Testing commands

```bash
# Enumerate Lambda functions and read their config (needs creds)
aws lambda list-functions --query 'Functions[].FunctionName'
aws lambda get-function-configuration --function-name target-fn

# Look for secrets leaked in environment variables
aws lambda get-function-configuration --function-name target-fn \
  --query 'Environment.Variables'

# Check the execution role's attached policies (privilege review)
aws iam list-attached-role-policies --role-name target-fn-role

# Invoke a function and capture the response
aws lambda invoke --function-name target-fn --payload '{}' out.json; cat out.json
```

## Tools

- [Prowler](Prowler.md) - flags over-privileged roles and public functions
- [cloudsplaining](https://github.com/salesforce/cloudsplaining) - IAM policy risk analysis
- [Trivy](Trivy.md) - scan function dependencies for known CVEs

## Mitigation

- Give each function a least-privilege IAM role scoped to exactly what it needs
- Store secrets in a secrets manager, not plaintext env vars
- Validate and sanitize every event source as untrusted input
- Keep dependencies patched and set sane timeouts/concurrency limits

## Resources

- [AWS Lambda security best practices](https://docs.aws.amazon.com/lambda/latest/dg/lambda-security.html)
- [OWASP Serverless Top 10](https://owasp.org/www-project-serverless-top-10/)

## Related

- [AWS](AWS.md)
- [Prowler](Prowler.md)
