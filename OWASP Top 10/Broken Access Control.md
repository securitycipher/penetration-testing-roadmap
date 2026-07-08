# Broken Access Control (A01:2021)

Broken Access Control is the **#1** risk in the 2021 OWASP Top 10. It happens when the app authenticates *who* you are but fails to properly enforce *what* you are allowed to do or see. The result: a normal user reads other users' data, reaches admin functions, or performs actions above their role.

**Authentication** = who are you. **Authorization (access control)** = what are you allowed to do. Broken Access Control is a failure of the second.

## Types of access control (and how they break)

- **Vertical** - a low-privilege user reaches high-privilege functions (a user hits `/admin`)
- **Horizontal** - a user reaches another user's data at the same level (IDOR - user A reads user B's invoice)
- **Context-dependent** - actions allowed out of order (skip payment, replay a one-time step)

## Common flaws to test

- **IDOR** - change an ID/reference to access others' objects (see [IDOR](../Vulnerabilities/IDOR.md))
- **Missing function-level authz** - admin endpoints only "hidden", not protected
- **Forced browsing** - guess/enumerate unlinked paths (`/admin`, `/api/v1/users`)
- **Parameter/role tampering** - `role=user` -> `role=admin`, `?admin=true`
- **Method tampering** - `GET` blocked but `POST`/`PUT`/`DELETE` allowed
- **Metadata tampering** - forge a JWT claim (`"role":"admin"`) or a cookie flag
- **CORS misconfig** - overly permissive origins expose authenticated APIs

## Step-by-step testing

1. Create **two accounts** (user A, user B) and note a third: an admin if possible.
2. Capture requests as A; replay them as B (or with no session) - do they still work?
3. Enumerate object references (`id`, `uuid`, filenames) across users.
4. Try to reach admin functions directly by URL and by HTTP method.
5. Tamper roles/claims in params, cookies, and JWTs.
6. Use **Burp Autorize** to automate "does this work with a lower-priv session?".

## Practical examples

```http
# Horizontal (IDOR) - read another user's data
GET /api/account?id=1002 HTTP/1.1
Authorization: Bearer <userA-token>     # 1002 belongs to userB

# Vertical - hit admin functionality directly
GET /admin/users HTTP/1.1
Cookie: session=<normal-user>

# Method tampering - UI only offers GET, but:
DELETE /api/posts/55 HTTP/1.1

# Role tampering
POST /api/profile HTTP/1.1
{"username":"me","role":"admin"}

# JWT claim tampering (if alg=none or weak secret)
# header {"alg":"none"} payload {"user":"me","role":"admin"}
```

## Tools

- [Burp Suite Autorize](https://github.com/PortSwigger/autorize) - auto-detect access-control gaps with a second session
- [Burp Intruder / ffuf](https://github.com/ffuf/ffuf) - enumerate IDs and hidden endpoints
- [jwt_tool](https://github.com/ticarpi/jwt_tool) - test JWT auth bypasses

```bash
# Forced browsing for hidden admin/API paths
ffuf -u https://target.tld/FUZZ -w /path/to/dirs.txt -H "Cookie: session=..." -mc 200,302
```

## Mitigation - the fix

- **Deny by default**; grant access explicitly per role/resource.
- Enforce authorization **server-side on every request**, checking object ownership:

```python
# SAFE - scope the query to the current user
invoice = Invoice.objects.get(id=req_id, owner=current_user)  # 404 if not theirs
```

- Don't trust client-supplied roles/IDs; derive identity from the session/token.
- Use unpredictable references (UUIDs) as defense in depth (not the fix).
- Log access-control failures and alert on anomalies.

## Practice

- [PortSwigger access control labs](https://portswigger.net/web-security/access-control)

## Reference

- [OWASP A01:2021 Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
