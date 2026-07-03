# Report Writing

A pentest report is the deliverable clients actually pay for. Technical skill without clear reporting does not land repeat business.

## Report structure

1. **Executive summary** - 1 page for leadership: overall risk, top 3 findings, business impact
2. **Scope and methodology** - what was tested, how, and any limitations
3. **Findings** - one section per issue with: title, severity, description, steps to reproduce, evidence, remediation
4. **Appendix** - raw scan output, tool versions, glossary

## Severity ratings

Use a consistent scale (CVSS or custom):

- **Critical** - unauthenticated RCE, full domain compromise, mass data breach
- **High** - auth bypass, significant data exposure, admin access
- **Medium** - stored XSS, IDOR on sensitive data, misconfigurations with partial impact
- **Low / Info** - missing headers, version disclosure, best-practice gaps

## Writing tips

- Lead with **impact**, not the vulnerability name. "Attacker can read any user's invoices" beats "IDOR found."
- Include **reproduction steps** a developer can follow without guessing.
- Give **fix guidance**, not just "patch it." Link to OWASP cheat sheets where relevant.

## Templates and examples

- [PCI DSS Reporting Template](https://www.pcisecuritystandards.org/)
- [SANS Pentest Report Template](https://www.sans.org/)
