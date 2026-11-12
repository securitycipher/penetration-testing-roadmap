# HTTP Fundamentals

Almost all web testing is just manipulating HTTP. Phase 2 of the roadmap assumes you understand requests, responses, methods, headers, status codes, and cookies before you open Burp Suite. If HTTP feels fuzzy, fix that first - everything in the [OWASP Top 10](../OWASP%20Top%2010/OWASP%20Top%2010.md) builds on it.

## Anatomy of a request

```http
POST /login HTTP/1.1
Host: target.tld
User-Agent: Mozilla/5.0
Content-Type: application/x-www-form-urlencoded
Cookie: session=abc123
Content-Length: 29

username=admin&password=hunter2
```

- **Request line:** method, path, version
- **Headers:** metadata (Host, Content-Type, Authorization, Cookie)
- **Body:** data for POST/PUT (form, JSON, multipart)

## Methods worth testing

- `GET` - retrieve; watch for sensitive data in the query string
- `POST` - submit data; where most actions happen
- `PUT` / `DELETE` - can expose [IDOR](../Vulnerabilities/IDOR.md) and access-control gaps
- `OPTIONS` - reveals allowed methods and [CORS](../Vulnerabilities/CORS%20Misconfiguration.md) policy
- `PATCH`, `HEAD`, `TRACE` - test anyway; servers mishandle them

## Status codes you will read all day

- **2xx** success (200 OK, 201 Created, 204 No Content)
- **3xx** redirects (301/302 - watch for [open redirect](../Vulnerabilities/Open%20Redirect.md))
- **4xx** client errors (401 auth needed, 403 forbidden, 404 not found, 429 rate-limited)
- **5xx** server errors (500 often means you broke something - good signal when fuzzing)

## Headers that matter to testers

- `Authorization` - Basic, Bearer (JWT), API keys
- `Set-Cookie` - flags like `HttpOnly`, `Secure`, `SameSite`
- `Content-Security-Policy`, `X-Frame-Options` - defenses to assess
- `Host` - target of [Host Header Injection](../Vulnerabilities/Host%20Header%20Injection.md)

## State: cookies, sessions, tokens

HTTP is stateless. Apps track you with session cookies or tokens. Understand how a session is created, where it lives, and what happens if you steal or forge it - that is the root of auth testing.

## Practice

- Intercept and edit requests in [Burp Suite](../Tools/Burp%20Suite.md)
- Replay requests with `curl -v` and read every header
- [PortSwigger Web Security Academy](../Labs/Web%20Security%20Academy.md) HTTP topics

## Related

- [Common Protocols](Common%20Protocols.md)
- [REST API Testing](../API%20Security/REST%20API%20Testing.md)
