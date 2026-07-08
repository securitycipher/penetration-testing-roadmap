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

## Finding template (copy/paste)

```markdown
### [HIGH] IDOR allows reading any user's invoices

**CVSS:** 7.5 (AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:N/A:N)

**Affected:** GET /api/v1/invoices/{id}

**Description:**
The invoice endpoint returns any invoice by numeric ID without checking
that it belongs to the authenticated user. An attacker can enumerate IDs
to read all customers' billing data.

**Steps to reproduce:**
1. Log in as user A and capture the request to /api/v1/invoices/1001.
2. Change the ID to 1002 (belongs to user B).
3. Observe the full invoice for user B is returned.

    curl -H "Authorization: Bearer <A_token>" \
      https://target.com/api/v1/invoices/1002

**Evidence:** [screenshot-1.png]

**Impact:** Full disclosure of all customers' PII and financial data.

**Remediation:** Enforce an ownership check server-side:
`WHERE invoice.id = :id AND invoice.user_id = :current_user`.
See OWASP Broken Access Control cheat sheet.
```

## CVSS scoring

```
Use the FIRST calculator: https://www.first.org/cvss/calculator/3.1
Key vectors: AV (attack vector), PR (privileges required),
UI (user interaction), C/I/A (confidentiality/integrity/availability impact).
```

## Common mistakes to avoid

- **Raw tool output as findings** — triage and validate; kill false positives.
- **No business context** — tie technical impact to what the client cares about.
- **Vague remediation** — give the exact fix, not "sanitize input."
- **Inconsistent severity** — apply the same scale to every finding.

## Templates and examples

- [PCI DSS Reporting Template](https://www.pcisecuritystandards.org/)
- [SANS Pentest Report Template](https://www.sans.org/)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
