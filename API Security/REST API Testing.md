# REST API Testing

APIs are the primary attack surface for modern apps. Testing differs from browser-based web testing - no cookies by default, JSON bodies, and machine clients.

## What to test

- **Authentication** - API keys in headers, JWT flaws, OAuth misconfigs
- **Authorization (BOLA/IDOR)** - change object IDs in `/api/users/123` to `/api/users/124`
- **Rate limiting** - brute force, OTP bypass, resource exhaustion
- **Mass assignment** - extra JSON fields (`"role":"admin"`) accepted by backend
- **Input validation** - injection in JSON fields, XML/SQL/NoSQL
- **Versioning** - old `/api/v1/` endpoints still live with weaker auth

## Burp workflow

1. Map all endpoints from Swagger/OpenAPI, JS files, or mobile app traffic
2. Send requests to Repeater; test auth removal, token tampering
3. Use **Autorize** extension for IDOR at scale
4. Fuzz parameters with Intruder or ffuf

## OWASP API Security Top 10 (2023) - what to map to

```text
API1  Broken Object Level Auth (BOLA/IDOR)   - the #1 API bug
API2  Broken Authentication
API3  Broken Object Property Level Auth       - mass assignment / excessive data exposure
API4  Unrestricted Resource Consumption       - no rate limits
API5  Broken Function Level Auth              - reach admin functions
API6  Unrestricted Access to Business Flows
API7  SSRF
API8  Security Misconfiguration
API9  Improper Inventory Management           - old /v1, undocumented endpoints
API10 Unsafe Consumption of 3rd-party APIs
```

## Quick tests

```bash
# Discover API routes / hidden versions
ffuf -u https://api.target.com/FUZZ -w api-wordlist.txt -mc 200,401,403
kr scan https://api.target.com -w routes-large.kite    # kiterunner

# BOLA/IDOR - swap object IDs across accounts
curl -H "Authorization: Bearer $TOKEN_A" https://api.target.com/invoices/1001
curl -H "Authorization: Bearer $TOKEN_A" https://api.target.com/invoices/1002  # someone else's?

# Broken function-level auth - hit admin route as a normal user
curl -H "Authorization: Bearer $USER_TOKEN" -X DELETE https://api.target.com/admin/users/5

# No auth at all?
curl https://api.target.com/invoices/1001
```

## Mass assignment (API3) example

```http
# Normal update
PATCH /api/users/me  {"name":"Bob"}

# Inject privileged fields the client shouldn't set
PATCH /api/users/me  {"name":"Bob","role":"admin","isVerified":true,"balance":99999}
```

If the backend blindly binds JSON to the model, you escalate. Also check **excessive data exposure**: does the response include fields the UI hides (password hashes, internal flags)?

## JWT / auth tests

```text
- alg:none  -> strip signature (Burp JWT Editor)
- Weak secret -> hashcat -m 16500 jwt.txt rockyou.txt, then forge claims
- Expired/none-verified token still accepted?
- Reuse another user's token; remove the Authorization header entirely
```

## Rate limiting (API4)

- Brute force login/OTP with Intruder - is there any throttling/lockout?
- Batch/parallel requests to bypass per-request limits.

## Tools

- [Burp Suite](https://portswigger.net/burp) + Autorize, JWT Editor
- [Postman](https://www.postman.com/) - collection-based testing
- [kiterunner](https://github.com/assetnote/kiterunner) - API route discovery
- [OWASP API Security Top 10](https://owasp.org/API-Security/)

## Deep dive

- [API Security - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [Web Application Security Checklist - API section](https://securitycipher.com/web-application-security-checklist/)
