# Rules of Engagement

Rules of engagement (RoE) define what testers can and cannot do. Breaking RoE can end a career or trigger legal action.

## What RoE typically covers

- **In-scope assets** - IP ranges, domains, apps, APIs, mobile builds
- **Out-of-scope** - production payment systems, third-party SaaS, social engineering targets
- **Allowed techniques** - brute force limits, DoS restrictions, data exfiltration caps
- **Testing windows** - business hours only vs 24/7
- **Emergency contacts** - who to call if you knock something over

## Legal basics

- Get **written authorization** (SOW, MSA, or signed letter) before any testing.
- Stay inside scope. "I found it so I tested it" is not a defense.
- Document everything: timestamps, requests, screenshots, impact.

## Bug bounty vs pentest RoE

| | Pentest | Bug Bounty |
|---|---------|------------|
| Scope | Contract-defined | Program policy |
| Timeline | Fixed engagement | Ongoing |
| Reporting | Formal PDF/report | Platform submission |
| Legal cover | SOW + NDA | Program terms |

## Resources

- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [PTES - Penetration Testing Execution Standard](http://www.pentest-standard.org/)
