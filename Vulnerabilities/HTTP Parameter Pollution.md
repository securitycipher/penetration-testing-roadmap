# What is HTTP Parameter Pollution?

HTTP Parameter Pollution (HPP) abuses the fact that different components handle duplicate parameters differently. Send `id=1&id=2` and one layer might read the first value, another the last, and a third might join them. When a security check and the business logic disagree on which value counts, you get filter bypass, WAF evasion, or logic flaws.

## How it works

- HTTP does not define what to do with repeated parameters
- Each stack picks its own rule (first, last, all-concatenated, or array)
- A front-end validates one value while the back-end acts on another

## Test payloads

```http
# Duplicate parameters - watch which value wins
GET /transfer?amount=100&amount=99999 HTTP/1.1

# Bypass a filter that only checks the first value
GET /search?q=safe&q=<script>alert(1)</script> HTTP/1.1

# Split a blocked payload across duplicates (concatenating stacks)
GET /x?p=UNION&p=SELECT HTTP/1.1
```

Common parsing behavior:

```text
PHP / Apache        -> last value
ASP.NET / IIS       -> comma-joined ("1,2")
JSP / Tomcat        -> first value
Node.js (express)   -> array ["1","2"]
```

## Tools

- [Burp Suite Repeater](https://portswigger.net/burp) - flip parameter order and observe
- [param-miner](https://github.com/PortSwigger/param-miner) - discover hidden/duplicated params

## Manual testing

1. Duplicate a sensitive parameter with two different values
2. Note which value the response acts on (first, last, or joined)
3. Where a WAF or client-side check exists, put the clean value where it looks and the payload where the app reads
4. Test both query string and POST body, and mixed sources

## Mitigation

- Normalize input: reject or explicitly handle duplicate parameters
- Read parameters from one source consistently, server-side
- Keep the validating layer and the consuming layer on the same parsing rules
- Do not trust WAF-only filtering for injection classes

## Deep dive

- [HTTP Parameter Pollution - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [OWASP Testing Guide: HPP](https://owasp.org/www-project-web-security-testing-guide/)

## CWE

- CWE-235: Improper Handling of Extra Parameters
