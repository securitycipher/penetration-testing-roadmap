# What are JWT Attacks?

JSON Web Tokens (JWTs) are signed tokens used for authentication and sessions. They are everywhere in modern APIs. The data inside is only Base64-encoded (not encrypted), and security depends entirely on the signature being verified correctly. Most JWT bugs come from servers that trust the token without properly checking that signature.

## Anatomy

A JWT is three Base64url parts separated by dots: `header.payload.signature`

```text
eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiYWRtaW4ifQ.<signature>
```

- **Header:** algorithm (`alg`) and type
- **Payload:** claims (`user`, `role`, `exp`)
- **Signature:** keyed hash of header+payload

Decode any token at the command line:

```bash
echo "eyJ1c2VyIjoiYWRtaW4ifQ" | base64 -d
```

## Common attacks

- **alg: none** - change the header to `{"alg":"none"}` and strip the signature. If the server accepts it, you can forge any claims.
- **Algorithm confusion (RS256 to HS256)** - the server expects an RSA-signed token but you re-sign with HS256 using the *public key* as the HMAC secret.
- **Weak HMAC secret** - brute-force the signing key offline, then forge tokens.
- **No signature verification** - some apps only decode the payload. Edit `role` to `admin` and resend.
- **kid injection** - abuse the `kid` header for path traversal or SQLi to control the key.
- **Missing `exp`** - tokens that never expire, so a stolen token is forever.

## Test payloads

```text
# Privilege escalation via unverified payload
{"user":"attacker","role":"admin"}

# alg confusion / none (use a tool to build these)
{"alg":"none","typ":"JWT"}
```

## Cracking a weak secret

```bash
hashcat -m 16500 jwt.txt rockyou.txt   # JWT HS256
# or
jwt_tool <token> -C -d rockyou.txt
```

## Tools

- [Burp Suite](../../Tools/Burp%20Suite.md) + the **JWT Editor** extension
- `jwt_tool` - all-in-one tampering, cracking, attack checks
- [Hashcat](../../Tools/Hashcat.md) mode 16500 for HS256 secrets

## Mitigation

- Verify the signature on every request and **pin the expected algorithm** server-side.
- Use strong, random secrets (HMAC) or proper key management (RSA/EC).
- Reject `alg: none`. Do not derive the HMAC key from a public key.
- Set and enforce short `exp`; support revocation for sensitive apps.

## CWE

- CWE-347: Improper Verification of Cryptographic Signature

## Deep dive

- [PortSwigger JWT labs](https://portswigger.net/web-security/jwt)
