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

- [Broken Access Control](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/OWASP%20Top%2010/Broken%20Access%20Control.md) (A01)
- [Cryptographic Failures](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/OWASP%20Top%2010/Cryptographic%20Failures.md) (A02)
- [Injection](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/OWASP%20Top%2010/Injection.md) (A03)
- [Insecure Design](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/OWASP%20Top%2010/Insecure%20Design.md) (A04)
- [Security Misconfiguration](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/OWASP%20Top%2010/Security%20Misconfiguration.md) (A05)
- [Vulnerable and Outdated Components](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/OWASP%20Top%2010/Vulnerable%20and%20Outdated%20Components.md) (A06)
- [Identification and Authentication Failures](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/OWASP%20Top%2010/Identification%20and%20Authentication%20Failures.md) (A07)
- [Software and Data Integrity Failures](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/OWASP%20Top%2010/Software%20and%20Data%20Integrity%20Failures.md) (A08)
- [Security Logging and Monitoring Failures](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/OWASP%20Top%2010/Security%20Logging%20and%20Monitoring%20Failures.md) (A09)
- [SSRF](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/OWASP%20Top%2010/SSRF.md) (A10)

## What is changing in the 2025 edition

OWASP published the **2025** Top 10 as a release candidate. The guides above still describe the 2021 list (which most tools and exams reference today), but you should know where things are heading:

- **New: A03 Software Supply Chain Failures** - a broader category that absorbs and expands the old "Vulnerable and Outdated Components" to cover the whole build/dependency/CI-CD chain.
- **New: Mishandling of Exceptional Conditions** - bugs from poor error, exception, and edge-case handling (fail-open logic, leaked stack traces, inconsistent states).
- **Broken Access Control stays #1.** Security Misconfiguration rises as cloud/IaC sprawl grows.
- **SSRF** is folded back under Broken Access Control rather than standing alone.
- **Cryptographic Failures** moves up, reflecting ongoing data-exposure incidents.

Action for testers: keep testing to the 2021 categories for now, but start tracking supply-chain and CI/CD attack surface explicitly. See [Software and Data Integrity Failures](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/OWASP%20Top%2010/Software%20and%20Data%20Integrity%20Failures.md) and the [Vulnerable and Outdated Components](https://github.com/securitycipher/penetration-testing-roadmap/blob/main/OWASP%20Top%2010/Vulnerable%20and%20Outdated%20Components.md) guide.

## How to use this as a pentester

1. Map the app (auth flows, roles, inputs, integrations).
2. Walk each category top-down - A01 access control usually yields the fastest wins.
3. For each finding, record: request, payload, impact, and remediation.
4. Cross-reference the [Vulnerabilities](https://github.com/securitycipher/penetration-testing-roadmap/tree/main/Vulnerabilities) folder for concrete payloads.

## Practice

- [PortSwigger Web Security Academy](https://portswigger.net/web-security) - free labs for nearly every category
- OWASP Juice Shop, DVWA, WebGoat

## Reference

- [OWASP Top 10 (2021) official](https://owasp.org/Top10/)
