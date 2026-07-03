# Penetration Testing Roadmap

**Last updated:** July 2026

A structured learning path from zero to junior penetration tester - topics, tools, labs, certifications, and hands-on guides.

**Live roadmap:** [securitycipher.com/penetration-testing-roadmap](https://securitycipher.com/penetration-testing-roadmap/)

## Quick start

1. Read [Intro.md](Intro.md) for the recommended learning path and TL;DR
2. Work through phases: **Foundations** → **Web** → **Infrastructure** → **Specialize** → **Labs & certs**
3. Open any topic below for a short guide with tools, labs, and links
4. See [FAQ.md](FAQ.md) for career and cert questions

## Learning path

| Phase | Focus | Time (part-time) |
|-------|-------|------------------|
| 1. Foundations | Linux, networking, scripting, crypto basics | 4-6 weeks |
| 2. Web security | HTTP, OWASP Top 10, Burp Suite, PortSwigger Academy | 6-8 weeks |
| 3. Infrastructure | AD basics, cloud, wireless, recon | 6-8 weeks |
| 4. Specialize | Web, cloud, mobile, API, or LLM track | Ongoing |
| 5. Prove it | HTB, TryHackMe, certs (eJPT, Security+, OSCP) | 3-6 months |

Full write-up: [Intro.md](Intro.md)

## Content index

### Getting started

- [Intro](Intro.md)
- [FAQ](FAQ.md)

### Foundations

- [Operating System](Operating%20System/)
- [Networking](Networking/)
- [Cryptography](Cryptography/)
- [Compliance](Compliance/)

### Core security

- [Terminology](Terminology/)
- [Vulnerabilities](Vulnerabilities/)
- [Security Testing Approaches](Security%20Testing%20Approaches/)
- [Threat Modeling](Threat%20modeling/)
- [Tools](Tools/)

### Web and application testing

- [OWASP Top 10](OWASP%20Top%2010/)
- [API Security](API%20Security/)
- [Methodology](Methodology/) - pentest phases, rules of engagement, reporting
- [Recon](Recon/) - OSINT, subdomains, JavaScript analysis

### Infrastructure and cloud

- [Active Directory](Active%20Directory/)
- [Cloud](Cloud/)
- [Containers](Containers/) - Docker and Kubernetes
- [Mobile](Mobile/) - Android and iOS testing

### Emerging areas

- [OWASP Top 10 LLM](OWASP%20Top%2010%20LLM/)

### Practice and credentials

- [Labs](Labs/)
- [Certifications](Certifications/)

## Related Security Cipher resources

- [Vulnerability Explain](https://securitycipher.com/vulnerability-explain/) - deep dives with payloads and tools
- [Secure Code Explain](https://securitycipher.com/secure-code-explain/) - fix vulnerabilities in code
- [Web Application Security Checklist](https://securitycipher.com/web-application-security-checklist/) - 100+ test cases
- [AWS Cloud Security Checklist](https://securitycipher.com/aws-cloud-security-checklist/)
- [OWASP Top 10 for LLM Applications](https://securitycipher.com/owasp-top-10-for-llm-applications/)
- [Penetration Testing Tricks](https://securitycipher.com/penetration-testing-tricks/)

## Certifications

| Cert | Guide |
|------|-------|
| CEH | [CEH.md](Certifications/CEH.md) |
| CISSP | [CISSP.md](Certifications/CISSP.md) |
| CompTIA Security+ | [CompTIA Security+.md](Certifications/CompTIA%20Security+.md) |
| OSCP | [OSCP.md](Certifications/OSCP.md) |
| OSWE | [OSWE.md](Certifications/OSWE.md) |
| OSWP | [OSWP.md](Certifications/OSWP.md) |
| eJPT | [eJPT.md](Certifications/eJPT.md) |
| PNPT | [PNPT.md](Certifications/PNPT.md) |
| CRTP | [CRTP.md](Certifications/CRTP.md) |
| BTL1 | [BTL1.md](Certifications/BTL1.md) |

## Labs

| Platform | Guide |
|----------|-------|
| Hack The Box | [HackTheBox.md](Labs/HackTheBox.md) |
| TryHackMe | [TryHackMe.md](Labs/TryHackMe.md) |
| pwn.college | [pwn.college.md](Labs/pwn.college.md) |
| VulHub | [VulHub.md](Labs/VulHub.md) |
| Web Security Academy | [Web Security Academy.md](Labs/Web%20Security%20Academy.md) |
| Root Me | [Root Me.md](Labs/Root%20Me.md) |
| Altoro Mutual | [Altoro Mutual.md](Labs/Altoro%20Mutual.md) |

## Contributing

Contributions are welcome. This repo is the source of truth for roadmap content.

1. **Fork** the repo and create a branch
2. **Add or edit** markdown under the right folder (one topic per file, clear headings, practical links)
3. **Open a pull request** with a short description of what you added or fixed

**Guidelines**

- Keep guides concise and actionable - labs, tools, and further reading where possible
- Match the tone of existing topics (see [Linux.md](Operating%20System/Linux.md) or [SQL Injection.md](Vulnerabilities/SQL%20Injection.md))
- Fix typos and broken links anytime - small PRs are fine
- New topics: pick the closest folder from [Content index](#content-index) above

All contributions are reviewed before merge. After merge, updates appear on the [live roadmap](https://securitycipher.com/penetration-testing-roadmap/) on the next publish cycle.

**Questions?** Open a [GitHub issue](https://github.com/securitycipher/penetration-testing-roadmap/issues).
