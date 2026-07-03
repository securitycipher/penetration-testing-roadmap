# What is Open Redirect?

An Open Redirect is a page that takes a URL from user input and redirects the browser there without validating the destination. On its own it is often rated low, but it is a reliable building block: it powers convincing phishing, steals OAuth tokens, and can be chained into SSRF or filter bypasses.

## How it works

- The app has a redirect parameter like `?next=`, `?url=`, `?returnTo=`
- It sends a `Location:` header (or JS redirect) using that value verbatim
- An attacker supplies an external URL and the victim lands on the attacker's site

## Test payloads

```text
# Straightforward
?next=https://evil.com
?url=//evil.com
?returnTo=https:evil.com

# Bypass naive "must start with /" checks
?next=/\evil.com
?next=https://target.tld.evil.com
?next=https://target.tld@evil.com

# Encoding tricks
?next=%2F%2Fevil.com
?next=https%3A%2F%2Fevil.com
```

## Tools

- [Burp Suite](https://portswigger.net/burp) - follow redirects and test bypasses
- [OpenRedireX](https://github.com/devanshbatham/OpenRedireX) - fuzz redirect params at scale
- [gf](https://github.com/tomnomnom/gf) - grep params likely to be redirects

## Manual testing

1. Find parameters named `url`, `next`, `redirect`, `return`, `dest`, `continue`
2. Point them at an external domain and see if the browser follows
3. If blocked, try `//`, `\/`, `@`, and encoded variants to defeat the allowlist
4. Look for it in OAuth/SSO flows where it can leak tokens via the fragment

## Mitigation

- Do not put user input in redirect targets; use server-side mapping keys
- If external redirects are needed, validate against a strict allowlist
- Force redirects to relative paths only, or show an interstitial warning
- Never reflect the raw parameter into the `Location` header

## Deep dive

- [Open Redirect - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [OWASP Unvalidated Redirects Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Unvalidated_Redirects_and_Forwards_Cheat_Sheet.html)

## CWE

- CWE-601: URL Redirection to Untrusted Site (Open Redirect)
