# Burp Suite

Burp Suite (PortSwigger) is the **industry-standard web app testing platform**. It sits between your browser and the target as an intercepting proxy, so you can view, modify, and replay every HTTP request. The free **Community** edition covers manual testing; **Professional** adds the automated scanner and Collaborator.

## The key tools (tabs)

- **Proxy** - intercept/modify traffic; everything flows through here
- **Repeater** - hand-edit and resend a single request (your main manual tool)
- **Intruder** - automate/fuzz requests with payload sets (brute force, enumeration)
- **Decoder** - encode/decode Base64, URL, hex, hashes
- **Comparer** - diff two responses
- **Sequencer** - measure token randomness/entropy
- **Collaborator** (Pro) - out-of-band detection (blind SSRF/XSS/SQLi)
- **Extensions (BApp Store)** - Autorize, Param Miner, Turbo Intruder, JWT Editor

## Setup - intercept HTTPS traffic

```text
1. Start Burp -> Proxy listener on 127.0.0.1:8080 (default)
2. Point your browser at that proxy (or use the built-in Burp browser)
3. Install Burp's CA cert: visit http://burp -> "CA Certificate" -> import into browser/OS
   (needed to intercept HTTPS without cert errors)
4. Proxy > Intercept ON to pause requests, or browse with it OFF and review HTTP history
```

Tip: the **built-in Chromium browser** (Proxy > Open Browser) is pre-configured - no cert hassle.

## Typical workflow

```text
1. Browse the whole app through Burp to populate Target > Site map + HTTP history.
2. Set target scope (right-click host > Add to scope) to cut noise.
3. Send interesting requests to Repeater (Ctrl+R) and probe manually.
4. Use Intruder (Ctrl+I) to fuzz params / brute force.
5. (Pro) Run an active scan on in-scope items.
6. Use Collaborator for blind/out-of-band findings.
```

## Intruder attack types

- **Sniper** - one payload set, one position at a time (most common)
- **Battering ram** - same payload in all positions
- **Pitchfork** - parallel sets (e.g. username[i] + password[i])
- **Cluster bomb** - every combination (full brute force of user x pass)

## Must-have extensions

- **Autorize** - automatic access-control / IDOR testing with a second session
- **Turbo Intruder** - high-speed requests, single-packet race conditions
- **Param Miner** - discover hidden params / cache-poisoning headers
- **JWT Editor** - test JWT signing/alg attacks
- **Logger++** - richer request logging

## Handy shortcuts

```text
Ctrl+R  send to Repeater      Ctrl+I  send to Intruder
Ctrl+Shift+R  go to Repeater  Ctrl+U/Shift+U  URL encode/decode selection
Ctrl+Space    send request (Repeater)
```

## Practice

- [PortSwigger Web Security Academy](https://portswigger.net/web-security) - free labs, best resource to learn Burp
