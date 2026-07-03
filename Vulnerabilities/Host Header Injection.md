# What is Host Header Injection?

Host Header Injection abuses apps that trust the incoming `Host` header and reflect it into responses, links, or logic. Since the client fully controls that header, an attacker can poison password-reset links, mess with caches, or reach internal virtual hosts. The classic high-impact case is account takeover through a poisoned reset email.

## How it works

- The app builds absolute URLs (reset links, redirects) from the `Host` header
- Or it makes a routing/access decision based on `Host`
- An attacker sets `Host: evil.com` and the app uses it as trusted input

## Test payloads

```http
# Point the app at an attacker domain
GET /reset-password HTTP/1.1
Host: evil.com

# Ambiguous host resolution
GET / HTTP/1.1
Host: target.tld
X-Forwarded-Host: evil.com

# Duplicate Host headers - which one wins?
GET / HTTP/1.1
Host: target.tld
Host: evil.com

# Try alternate routing headers
X-Host: evil.com
X-Forwarded-Server: evil.com
```

## Tools

- [Burp Suite](https://portswigger.net/burp) - edit the Host header freely in Repeater
- [Param Miner](https://github.com/PortSwigger/param-miner) - discover header-based cache poisoning

## Manual testing

1. Change the `Host` header and see if it appears in the response body or a redirect
2. Trigger a password reset and inspect the link domain in the email
3. Try `X-Forwarded-Host`, `X-Host`, and duplicate `Host` headers
4. Check whether a poisoned response gets cached and served to others

## Mitigation

- Validate `Host` against an allowlist of expected domains
- Build absolute URLs from server-side config, not the request header
- Ignore `X-Forwarded-Host` unless a trusted proxy sets it
- Configure a strict default vhost so unknown hosts are rejected

## Deep dive

- [Host Header Injection - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger Host header attacks](https://portswigger.net/web-security/host-header)

## CWE

- CWE-644: Improper Neutralization of HTTP Headers for Scripting Syntax
- CWE-20: Improper Input Validation
