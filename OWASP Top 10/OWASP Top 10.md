# OWASP Top 10

OWASP (Open Worldwide Application Security Project) publishes the **OWASP Top 10** - a ranked list of the most critical web application security risks, updated roughly every 3-4 years from real-world data. It is the industry-standard starting point for both developers (what to defend) and pentesters (what to test). This page covers the **2021** edition.

## The 2021 list at a glance

| Rank | Category | In short |
|------|----------|----------|
| A01 | **Broken Access Control** | Users do things/see data they shouldn't (IDOR, missing authz) |
| A02 | **Cryptographic Failures** | Weak/missing crypto exposes sensitive data (was "Sensitive Data Exposure") |
| A03 | **Injection** | Untrusted input changes a query/command (SQLi, XSS, command injection) |
| A04 | **Insecure Design** | Missing security controls by design, not just bugs (new in 2021) |
| A05 | **Security Misconfiguration** | Default creds, verbose errors, open cloud buckets (now includes XXE) |
| A06 | **Vulnerable and Outdated Components** | Using libraries/frameworks with known CVEs |
| A07 | **Identification and Authentication Failures** | Weak login, session, MFA (was "Broken Authentication") |
| A08 | **Software and Data Integrity Failures** | Insecure deserialization, unsigned updates, CI/CD tampering |
| A09 | **Security Logging and Monitoring Failures** | Attacks go undetected |
| A10 | **Server-Side Request Forgery (SSRF)** | Server is tricked into making requests for the attacker |

## What changed from 2017 -> 2021

- **New:** A04 Insecure Design, A08 Software & Data Integrity Failures, A10 SSRF
- **Merged/renamed:** XXE folded into A05 Security Misconfiguration; "Sensitive Data Exposure" -> A02 Cryptographic Failures; "Broken Authentication" -> A07
- **Moved up:** Broken Access Control jumped to #1 (most common serious issue found)

## Detailed guides in this folder

- [Broken Access Control](Broken%20Access%20Control.md) (A01)
- [Cryptographic Failures](Cryptographic%20Failures.md) (A02)
- [Injection](Injection.md) (A03)
- [Insecure Design](Insecure%20Design.md) (A04)
- [Security Misconfiguration](Security%20Misconfiguration.md) (A05)
- [Vulnerable and Outdated Components](Vulnerable%20and%20Outdated%20Components.md) (A06)
- [Identification and Authentication Failures](Identification%20and%20Authentication%20Failures.md) (A07)
- [Software and Data Integrity Failures](Software%20and%20Data%20Integrity%20Failures.md) (A08)
- [Security Logging and Monitoring Failures](Security%20Logging%20and%20Monitoring%20Failures.md) (A09)
- [SSRF](SSRF.md) (A10)

## How to use this as a pentester

1. Map the app (auth flows, roles, inputs, integrations).
2. Walk each category top-down - A01 access control usually yields the fastest wins.
3. For each finding, record: request, payload, impact, and remediation.
4. Cross-reference the [Vulnerabilities](../Vulnerabilities/) folder for concrete payloads.

## Practice

- [PortSwigger Web Security Academy](https://portswigger.net/web-security) - free labs for nearly every category
- OWASP Juice Shop, DVWA, WebGoat

## Reference

- [OWASP Top 10 (2021) official](https://owasp.org/Top10/)
