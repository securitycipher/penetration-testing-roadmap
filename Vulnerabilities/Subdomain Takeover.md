# What is Subdomain Takeover?

Subdomain takeover happens when a DNS record (usually a CNAME) points to an external service that is no longer claimed - an abandoned cloud bucket, a deleted Heroku app, an expired SaaS page. An attacker registers that service under their own account, and because the victim's subdomain still points at it, they now control content served from `sub.victim.com`. It is a common, high-impact bug-bounty finding.

## How it happens

1. A team sets `blog.victim.com` as a CNAME to `victim.github.io` (or an S3 bucket, Heroku app, etc.).
2. They later delete the GitHub Pages site / bucket but **forget to remove the DNS record**.
3. The subdomain now points to an unclaimed resource (a "dangling" record).
4. An attacker claims that resource name on the provider and serves their own content from the victim's subdomain.

## Impact

- Serve phishing or malware from a trusted domain
- Steal cookies scoped to the parent domain; bypass some CORS/SSO trust
- Host content for further attacks with the victim's reputation and TLS

## How to find it

1. Enumerate subdomains (see [Subdomain Enumeration](../Recon/Subdomain%20Enumeration.md)).
2. Resolve each and inspect CNAMEs pointing to third-party services.
3. Look for **fingerprints** of unclaimed services in the HTTP response, e.g.:

```text
"There isn't a GitHub Pages site here."        -> GitHub Pages
"NoSuchBucket"                                  -> AWS S3
"The specified bucket does not exist"           -> S3
"No such app" / "herokucdn" error               -> Heroku
"Do you want to register *.wordpress.com?"      -> WordPress
```

4. If the target service is claimable, register it (in scope only) and prove control with a benign file.

## Tools

- `subfinder` / `amass` for subdomain discovery
- **subjack**, **nuclei** (takeover templates), **can-i-take-over-xyz** (fingerprint reference)
- [Nuclei](../../Tools/Nuclei.md): `nuclei -t http/takeovers/ -l subs.txt`

## Mitigation

- Remove DNS records the moment the backing resource is decommissioned (make it part of teardown).
- Audit DNS for dangling CNAMEs regularly.
- Claim/park wildcard and service resources you depend on.
- Monitor for unresolved or newly unclaimed targets.

## CWE

- CWE-350: Reliance on Reverse DNS Resolution (related misconfiguration class)

## Deep dive

- [can-i-take-over-xyz](https://github.com/EdOverflow/can-i-take-over-xyz)
- [Subdomain Enumeration](../Recon/Subdomain%20Enumeration.md)
