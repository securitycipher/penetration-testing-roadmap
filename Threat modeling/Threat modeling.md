# Threat modeling
Threat modeling is a structured approach to identifying and evaluating potential security threats in a system or application. It helps in understanding the potential vulnerabilities and risks that may exist, allowing organizations to implement effective security measures to protect their assets.

Here's a breakdown of key concepts related to threat modeling:

- Define the System
  - Identify and define the boundaries of the system or application you want to assess.
  - Understand the purpose, functionality, and components of the system.
- Identify Assets
  - Determine the valuable assets within the system, such as data, hardware, software, or intellectual property.
  - Assets could be customer information, financial data, proprietary algorithms, etc.
- Enumerate Threats
  - List potential threats that could exploit vulnerabilities in the system.
  - Threats could include unauthorized access, data breaches, malware, physical theft, etc.
- Identify Vulnerabilities
  - Explore the weaknesses or vulnerabilities in the system that could be exploited by threats.
  - Vulnerabilities might be insecure authentication, weak encryption, unpatched software, etc.
- Assess Risks
  - Evaluate the likelihood and impact of each threat exploiting a vulnerability.
  - Prioritize risks based on their potential impact on the system and its assets.
- Mitigation Strategies
  - Devise and implement strategies to mitigate or eliminate identified risks.
  - This could involve implementing security controls, using encryption, updating software regularly, etc.
- Iterative Process
  - Threat modeling is not a one-time activity; it should be an iterative process.
  - As the system evolves or new threats emerge, the threat model should be revisited and updated.
- Tools and Frameworks
  - Various tools and frameworks are available to assist in threat modeling, such as STRIDE (Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, Elevation of privilege) and DREAD (Damage, Reproducibility, Exploitability, Affected users, Discoverability).
- Documentation
  - Document the entire threat modeling process, including identified threats, vulnerabilities, and mitigation strategies.
  - This documentation serves as a reference for developers, security teams, and other stakeholders.
- Collaboration
  - Involve stakeholders from different departments (development, operations, security) in the threat modeling process.
  - Collaboration ensures a holistic understanding of the system and its potential risks.

Threat modeling is a crucial step in building and maintaining secure systems. It helps organizations proactively address security concerns and minimize the likelihood and impact of potential threats. By incorporating threat modeling into the development lifecycle, businesses can enhance the overall security posture of their systems and protect sensitive information from unauthorized access and exploitation.

---

## The four key questions (Shostack framework)

Every threat model boils down to answering these:

1. **What are we building?** → draw a data-flow diagram (DFD).
2. **What can go wrong?** → apply [STRIDE](STRIDE.md) / attack trees.
3. **What are we going to do about it?** → mitigations/controls.
4. **Did we do a good enough job?** → validate (often with a [pentest](../Methodology/Pentest%20Phases.md)).

## Worked mini-example: a login feature

```
[User] --creds--> (Login Process) --query--> [User DB]
                        |
                   trust boundary (internet -> app)
```

| Element | Threat (STRIDE) | Mitigation |
|---------|-----------------|------------|
| Creds in transit | Information disclosure | TLS 1.2+, HSTS |
| Login process | Spoofing (brute force) | Rate limit + MFA + lockout |
| Login process | Tampering (SQLi) | Parameterized queries |
| User DB | Information disclosure | Encrypt at rest, least-priv DB user |
| Auth events | Repudiation | Audit logging |

## Frameworks and tools

| Framework | Best for |
|-----------|----------|
| [STRIDE](STRIDE.md) | Per-component threat enumeration (dev-friendly) |
| [PASTA](PASTA.md) | Risk-centric, business-aligned, 7 stages |
| DREAD | Risk scoring (Damage/Reproducibility/Exploitability/Affected/Discoverability) |
| Attack Trees | Modeling attacker goals and paths |

```bash
# Popular tooling
# - OWASP Threat Dragon (free, diagram + STRIDE)
# - Microsoft Threat Modeling Tool (free)
# - threagile (threat modeling as code / YAML)
```

## Threat modeling vs pentesting

Threat modeling is **proactive and design-time** ("what *could* go wrong"); a pentest is **reactive and runtime** ("what *actually* is wrong"). A good threat model tells the pentester exactly where to look.

## Related

- [STRIDE](STRIDE.md) · [PASTA](PASTA.md)
- [Pentest Phases](../Methodology/Pentest%20Phases.md)
