# What is Clickjacking?

Clickjacking (UI redress) tricks a user into clicking something different from what they think they are clicking. The attacker loads the target site in a transparent iframe over a decoy page, so a click on the decoy actually hits a sensitive button on the target - "delete account", "authorize payment", "grant OAuth". It only works when the target lets itself be framed.

## How it works

- Attacker page frames the target site with `opacity: 0`
- A decoy button is positioned exactly under the real target button
- The victim clicks the decoy and the click lands on the framed site

## Proof-of-concept

```html
<style>
  iframe {
    position: absolute; top: 0; left: 0;
    width: 800px; height: 600px;
    opacity: 0.0;          /* invisible overlay; set to 0.2 while testing */
    z-index: 2;
  }
  #decoy {
    position: absolute; top: 220px; left: 310px;
    z-index: 1;
  }
</style>

<button id="decoy">Click here to win!</button>
<iframe src="https://target.tld/account/delete"></iframe>
```

## Tools

- [Burp Suite Clickbandit](https://portswigger.net/burp) - auto-generates a clickjacking PoC
- Browser DevTools - verify response headers (`X-Frame-Options`, CSP `frame-ancestors`)

## Manual testing

1. Try to load the target page inside an `<iframe>` on your own page
2. If it renders, framing is allowed - check response headers to confirm
3. Overlay a decoy and align it with a sensitive action
4. Set iframe opacity to 0 to demonstrate the invisible-click impact

## Mitigation

- Send `Content-Security-Policy: frame-ancestors 'self'` (the modern control)
- Also send `X-Frame-Options: DENY` or `SAMEORIGIN` for older browsers
- Require re-authentication or confirmation for destructive actions
- Use `SameSite` cookies so framed requests do not carry the session

## Deep dive

- [Clickjacking - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [OWASP Clickjacking Defense Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html)

## CWE

- CWE-1021: Improper Restriction of Rendered UI Layers or Frames
