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

## Quick tests

```bash
# Discover API routes
ffuf -u https://api.target.com/FUZZ -w api-wordlist.txt -mc 200,401,403

# JWT alg none attack (manual in Burp JWT Editor)
# Change header: {"alg":"none"} and strip signature

# IDOR check
curl -H "Authorization: Bearer $TOKEN" https://api.target.com/invoices/1001
curl -H "Authorization: Bearer $TOKEN" https://api.target.com/invoices/1002
```

## Tools

- [Burp Suite](https://portswigger.net/burp) + Autorize, JWT Editor
- [Postman](https://www.postman.com/) - collection-based testing
- [kiterunner](https://github.com/assetnote/kiterunner) - API route discovery
- [OWASP API Security Top 10](https://owasp.org/API-Security/)

## Deep dive

- [API Security - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [Web Application Security Checklist - API section](https://securitycipher.com/web-application-security-checklist/)
