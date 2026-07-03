# What is HTTP Request Smuggling?

HTTP Request Smuggling happens when a front-end (proxy, load balancer, CDN) and a back-end server disagree about where one request ends and the next begins. The attacker crafts a request that the front-end sees as one message and the back-end splits into two, smuggling a hidden request in front of the next user's traffic. Impact ranges from cache poisoning to stealing other users' requests and bypassing front-end auth.

## The core desync

- `Content-Length` (CL) says the body is N bytes
- `Transfer-Encoding: chunked` (TE) says the body ends at a zero-size chunk
- If two servers prioritize different headers, the boundary desyncs

## Variants and payloads

```http
# CL.TE - front-end uses Content-Length, back-end uses Transfer-Encoding
POST / HTTP/1.1
Host: target.tld
Content-Length: 6
Transfer-Encoding: chunked

0

G
```

```http
# TE.CL - front-end uses Transfer-Encoding, back-end uses Content-Length
POST / HTTP/1.1
Host: target.tld
Content-Length: 4
Transfer-Encoding: chunked

5c
GPOST / HTTP/1.1
...
0

```

## Tools

- [HTTP Request Smuggler](https://github.com/PortSwigger/http-request-smuggler) - Burp extension by James Kettle
- [Burp Suite Repeater](https://portswigger.net/burp) - disable "Update Content-Length" to test manually
- [smuggler](https://github.com/defparam/smuggler) - CLI desync detection

## Manual testing

1. Use the Smuggler Burp extension to run the detection probes first
2. Confirm a timing differential (a smuggled prefix delays the next response)
3. Build a CL.TE or TE.CL payload based on which desync fired
4. Escalate carefully - smuggling can affect real users, so keep it in scope and low-noise

## Mitigation

- Use HTTP/2 end to end and reject downgrades that reintroduce the ambiguity
- Make front-end and back-end normalize headers identically
- Reject requests that contain both `Content-Length` and `Transfer-Encoding`
- Keep proxies/CDNs patched; this class evolves quickly

## Deep dive

- [HTTP Request Smuggling - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger request smuggling labs](https://portswigger.net/web-security/request-smuggling)

## CWE

- CWE-444: Inconsistent Interpretation of HTTP Requests (HTTP Request Smuggling)
