# What is Session Fixation?

Session Fixation is an attack where the attacker sets or knows a victim's session identifier *before* they log in, then rides that same session once the victim authenticates. The root cause is an app that does not issue a fresh session ID at login - it keeps the pre-auth one and simply marks it authenticated.

## How it works

- Attacker obtains a valid session ID (the app may hand one out to anyone)
- Attacker plants that ID in the victim's browser (URL param, cookie, or a set-cookie via XSS)
- Victim logs in; the app reuses the same ID
- Attacker, holding the same ID, is now logged in as the victim

## Session Fixation vs Session Hijacking

- **Hijacking** = steal a token *after* the victim logs in.
- **Fixation** = give the victim a token you already know *before* they log in, and the app keeps it.

## Attack walkthrough

1. You visit `target.tld` and get session ID `ABC123` (or you set one you choose).
2. You send the victim a link that plants that ID: `https://target.tld/?PHPSESSID=ABC123`.
3. The victim clicks it and logs in with their real credentials.
4. The app does **not** rotate the ID - session `ABC123` is now authenticated as the victim.
5. You use `ABC123` from your browser and are logged in as them.

## Test payloads

```http
# App accepts a session ID from the URL (a red flag)
https://target.tld/login;jsessionid=ATTACKERKNOWNVALUE
https://target.tld/?PHPSESSID=attacker_known_value

# Force a cookie value client-side if XSS exists
document.cookie = "SESSIONID=attacker_known_value; path=/";
```

## Tools

- [Burp Suite](https://portswigger.net/burp) - compare the session cookie before and after login
- Browser DevTools - inspect `Set-Cookie` on the login response

## Manual testing

1. Note the session cookie value **before** logging in.
2. Log in, then note the cookie again.
3. **Same value = vulnerable** (the app did not regenerate the ID).
4. Confirm end-to-end: set a known pre-auth ID, log in, reuse it from another client.

## Mitigation - the fix

- **Regenerate the session ID at login** and on every privilege change:

```php
// PHP - rotate the session on authentication
session_regenerate_id(true);   // true = delete the old session
```

```python
# Django rotates the session key automatically on login() - don't disable it
```

- Never accept session IDs from URL parameters.
- Set cookies `HttpOnly`, `Secure`, `SameSite`.
- Expire sessions promptly (idle + absolute timeout).

## Practice

- OWASP WebGoat session management lessons

## Deep dive

- [Session Fixation - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)

## CWE

- CWE-384: Session Fixation
