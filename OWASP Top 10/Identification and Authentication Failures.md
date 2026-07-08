# Identification and Authentication Failures (A07:2021)

Formerly "Broken Authentication", this category covers weaknesses in how an app verifies **who** a user is and manages their session. When login, password recovery, session handling, or MFA is weak, attackers take over accounts.

## What to test

- **Weak/enumerable credentials** - no rate limiting, weak password policy, default accounts
- **Username enumeration** - login/reset/register reveals which usernames exist
- **Credential stuffing / brute force** - reused breach passwords, no lockout/MFA
- **Broken password reset** - guessable tokens, host-header poisoning, no expiry
- **Weak session management** - no rotation on login, no logout invalidation (see [Session Hijacking](../Vulnerabilities/Session%20Hijacking.md))
- **MFA flaws** - can be skipped, brute-forced, or bypassed via a different endpoint
- **JWT flaws** - `alg:none`, weak secret, no signature check

## Step 1 - Username enumeration

Compare responses to spot valid vs invalid users:

```text
Login:  "Invalid username"  vs  "Invalid password"     -> enumerable
Reset:  "Email sent"        vs  "No such user"          -> enumerable
Also compare: response time, status code, redirect, subtle wording
```

## Step 2 - Brute force / credential stuffing

```bash
# Hydra against an HTTP POST login form
hydra -l admin -P rockyou.txt target.tld http-post-form \
  "/login:username=^USER^&password=^PASS^:Invalid password"

# Burp Intruder: Sniper (one field) or Pitchfork/Cluster bomb (user+pass lists)
```

Watch for: no lockout, no CAPTCHA, no rate limit, no MFA -> the app is brute-forceable.

## Step 3 - JWT attacks

```bash
# Analyse and attack a token
jwt_tool eyJ...token...

# 1) alg:none - strip the signature
# header {"alg":"none","typ":"JWT"}  ->  server accepts unsigned token

# 2) Weak HMAC secret - crack it, then forge any claims
hashcat -m 16500 jwt.txt rockyou.txt

# 3) alg confusion (RS256 -> HS256) using the public key as the HMAC secret
jwt_tool <token> -X k -pk public.pem
```

## Step 4 - Password reset abuse

- Reset token predictable/short -> guess it.
- `Host`/`X-Forwarded-Host` poisoning -> reset link points to your server (see [Host Header Injection](../Vulnerabilities/Host%20Header%20Injection.md)).
- Token doesn't expire / is reusable / not tied to the user.

## Tools

- [Hydra](https://github.com/vanhauser-thc/thc-hydra), [Burp Intruder](https://portswigger.net/burp) - brute force
- [jwt_tool](https://github.com/ticarpi/jwt_tool) - JWT testing
- [hashcat](https://hashcat.net/) - crack hashes / JWT secrets

## Mitigation - the fix

- Enforce **strong passwords** (length-based), check against breach lists (HIBP).
- **Rate limit** logins, add **lockout/CAPTCHA**, and require **MFA**.
- Return **generic** login/reset messages (no enumeration).
- Rotate session IDs on login; invalidate on logout server-side.
- Sign JWTs with a strong secret/asymmetric key; **verify the signature and `alg`** server-side; short expiry.
- Reset tokens: random (128-bit), single-use, short-lived, bound to the user.

## Practice

- [PortSwigger authentication labs](https://portswigger.net/web-security/authentication) and [JWT labs](https://portswigger.net/web-security/jwt)

## Reference

- [OWASP A07:2021](https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/)
