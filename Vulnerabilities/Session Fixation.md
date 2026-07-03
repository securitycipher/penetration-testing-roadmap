# What is Session Fixation?

Session Fixation is an attack where the attacker sets or knows a victim's session identifier *before* they log in, then rides that same session once the victim authenticates. The root cause is an app that does not issue a fresh session ID at login - it keeps the pre-auth one and simply marks it authenticated.

## How it works

- Attacker obtains a valid session ID (the app may hand one out to anyone)
- Attacker plants that ID in the victim's browser (URL param, cookie, or a set-cookie via XSS)
- Victim logs in; the app reuses the same ID
- Attacker, holding the same ID, is now logged in as the victim

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

1. Capture the session cookie value before logging in
2. Log in and capture the cookie again
3. If the value is identical, the app is likely vulnerable (no rotation)
4. Confirm by setting a known pre-auth ID, logging in, and reusing it from another client

## Mitigation

- Regenerate the session ID on every privilege change, especially at login
- Never accept session IDs from URL parameters
- Set cookies `HttpOnly`, `Secure`, and `SameSite`
- Bind sessions to additional context and expire them promptly

## Deep dive

- [Session Fixation - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)

## CWE

- CWE-384: Session Fixation
