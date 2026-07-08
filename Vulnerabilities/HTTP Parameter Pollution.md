# What is HTTP Parameter Pollution?

HTTP Parameter Pollution (HPP) abuses the fact that different components handle duplicate parameters differently. Send `id=1&id=2` and one layer might read the first value, another the last, and a third might join them. When a security check and the business logic disagree on which value counts, you get filter bypass, WAF evasion, or logic flaws.

## How it works

- HTTP does not define what to do with repeated parameters
- Each stack picks its own rule (first, last, all-concatenated, or array)
- A front-end validates one value while the back-end acts on another

## Parser behaviour cheat sheet (which value wins)

```text
PHP / Apache        -> LAST value          (amount=100&amount=99999 => 99999)
ASP / IIS           -> comma-joined        ("100,99999")
ASP.NET             -> comma-joined
JSP / Tomcat        -> FIRST value         (=> 100)
Python Flask        -> FIRST value
Node.js (express)   -> ARRAY ["100","99999"]
```

Knowing this lets you place the "clean" value where the validator reads, and the "evil" value where the business logic reads.

## Test payloads

```http
# Duplicate parameters - watch which value wins
GET /transfer?amount=100&amount=99999 HTTP/1.1

# WAF/filter only checks the first value; app uses the last (or vice versa)
GET /search?q=safe&q=<script>alert(1)</script> HTTP/1.1

# Split a blocked keyword across duplicates on concatenating stacks
GET /x?p=UNION&p=SELECT HTTP/1.1

# Access control: validator sees user, backend sees admin
POST /api/update  role=user&role=admin
```

## Scenarios where HPP wins

- **WAF bypass** - front-end WAF inspects the first `q`, app uses the last.
- **Business logic** - price/quantity/amount read differently by two components.
- **Access control** - the check and the action read different values.
- **OTP/2FA** - duplicate `otp` params to skip verification on some stacks.

## Full walkthrough

1. `POST /transfer` with `amount=100` succeeds normally.
2. Send `amount=100&amount=100000`. The validator (Java) reads the first (`100`, allowed), but the DB layer (a separate PHP microservice) reads the last (`100000`).
3. The transfer of 100000 goes through. HPP confirmed.

## Tools

- [Burp Suite Repeater](https://portswigger.net/burp) - flip parameter order and observe
- [param-miner](https://github.com/PortSwigger/param-miner) - discover hidden/duplicated params

## Mitigation - the fix

- **Reject or explicitly handle** duplicate parameters (don't silently pick one).
- Read parameters from **one source** consistently, server-side.
- Keep the validating layer and consuming layer on the **same parsing rules**.
- Never rely on WAF-only filtering for injection classes.

## Practice

- OWASP WebGoat; portswigger community write-ups

## Deep dive

- [HTTP Parameter Pollution - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [OWASP Testing Guide: HPP](https://owasp.org/www-project-web-security-testing-guide/)

## CWE

- CWE-235: Improper Handling of Extra Parameters
