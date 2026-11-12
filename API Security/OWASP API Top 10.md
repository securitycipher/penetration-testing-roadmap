# OWASP API Security Top 10 (2023)

APIs now carry most of the traffic behind web and mobile apps, and they fail differently than classic web apps. OWASP maintains a **separate** Top 10 just for APIs because the big risks are about authorization and business logic, not injection. If you test REST or GraphQL, this is the list to work through. The current edition is **2023**.

## The 2023 list

| ID | Risk | In short |
|----|------|----------|
| API1 | **Broken Object Level Authorization (BOLA)** | The API-specific [IDOR](../Vulnerabilities/IDOR.md): swap an object ID, get another user's data. #1 by far. |
| API2 | **Broken Authentication** | Weak tokens, no rate limit on login, flawed [JWT](../Vulnerabilities/JWT%20Attacks.md) handling. |
| API3 | **Broken Object Property Level Authorization** | Merges old "excessive data exposure" + [mass assignment](../Vulnerabilities/Mass%20Assignment.md): read/write fields you should not. |
| API4 | **Unrestricted Resource Consumption** | No rate/size limits -> DoS and cost abuse. |
| API5 | **Broken Function Level Authorization** | Call admin endpoints as a normal user (change `GET /users` to `DELETE /admin/users`). |
| API6 | **Unrestricted Access to Sensitive Business Flows** | Automating a flow (checkout, signup) the business did not expect - see [Business Logic](../Vulnerabilities/Business%20Logic%20Vulnerabilities.md). |
| API7 | **Server-Side Request Forgery (SSRF)** | API fetches an attacker-supplied URL. |
| API8 | **Security Misconfiguration** | Verbose errors, missing [CORS](../Vulnerabilities/CORS%20Misconfiguration.md)/headers, default settings. |
| API9 | **Improper Inventory Management** | Forgotten `/v1/`, staging, and shadow APIs with weaker auth. |
| API10 | **Unsafe Consumption of APIs** | Trusting third-party APIs blindly (the API as a client). |

## How to test (priority order)

1. **BOLA/BFLA first.** Create two accounts, capture requests, swap IDs and roles, test every verb. This finds the most bugs.
2. **Enumerate the surface.** Pull the OpenAPI/Swagger spec, find old versions (API9), and map every endpoint.
3. **Attack auth.** Inspect tokens, test for weak JWTs, missing expiry, and no login rate limiting.
4. **Property-level checks.** Send extra fields (`"isAdmin":true`) and look for fields you should not see in responses.
5. **Resource limits.** Hammer endpoints, send huge payloads, deep pagination.

## Test payloads

```http
# BOLA - swap the object ID with another user's
GET /api/v1/users/1002/orders HTTP/1.1
Authorization: Bearer <your-token>

# BFLA - hit an admin function as a normal user
DELETE /api/v1/admin/users/55 HTTP/1.1

# Property-level - inject privileged fields (mass assignment)
PATCH /api/v1/users/me  {"email":"x@x.com","role":"admin","verified":true}
```

## Tools

- [Burp Suite](../Tools/Burp%20Suite.md) + the Autorize extension (auto-detects BOLA/BFLA)
- [REST API Testing](REST%20API%20Testing.md) and [GraphQL Security](GraphQL%20Security.md) guides
- Postman / `ffuf` for endpoint and version enumeration
- Practice target: [crAPI](../Labs/crAPI.md)

## Mitigation

- Enforce **object-level and function-level authorization** on every request, server-side.
- Use allowlists for writable properties; never bind request bodies straight to models.
- Rate-limit and cap payload/response sizes.
- Keep an API inventory; retire old versions and lock down non-prod.

## Resources

- [OWASP API Security Top 10 (2023)](https://owasp.org/API-Security/)
