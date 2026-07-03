# What is IDOR?

Insecure Direct Object Reference (IDOR) is a broken access control bug where the app exposes a reference to an object (a numeric ID, filename, or UUID) and does not check that the current user is allowed to access it. Change the reference, get someone else's data. It is one of the most common and highest-impact findings in bug bounty because it is easy to miss and easy to exploit.

## How it works

- Objects are addressed by predictable references like `?id=123` or `/invoice/123.pdf`
- The app authenticates you but forgets to authorize the specific object
- Swapping the reference returns data or actions belonging to another user

## Test payloads

```http
# Baseline as your own user
GET /api/account?id=1001 HTTP/1.1
Authorization: Bearer <your-token>

# Increment / decrement to reach other users
GET /api/account?id=1002 HTTP/1.1

# Try other verbs on the same object
PUT /api/account/1002 HTTP/1.1
DELETE /api/orders/58213 HTTP/1.1
```

```text
# Reference styles worth fuzzing
id=1000..2000            numeric enumeration
uuid                     leaked in other responses? not truly random?
base64(user@site)        decode, edit, re-encode
filename=report_123.pdf  path/predictable names
```

## Tools

- [Burp Suite Autorize](https://github.com/PortSwigger/autorize) - auto-detects access control gaps
- [Burp Intruder / ffuf](https://portswigger.net/burp) - enumerate IDs at scale
- [Authz plugins] - replay requests with a second, lower-privileged session

## Manual testing

1. Create two accounts (user A and user B)
2. Capture a request that returns A's object and note the reference
3. Replay it with B's session, or just swap A's ID for another
4. Test every verb (GET/POST/PUT/DELETE) and nested objects, not just reads

## Mitigation

- Enforce object-level authorization on every request, server-side
- Scope queries to the session user (`WHERE owner_id = current_user`)
- Use unpredictable references (random UUIDs) as defense in depth, not the fix
- Log and alert on access-control anomalies

## Deep dive

- [IDOR - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger access control labs](https://portswigger.net/web-security/access-control)

## CWE

- CWE-639: Authorization Bypass Through User-Controlled Key
- CWE-284: Improper Access Control
