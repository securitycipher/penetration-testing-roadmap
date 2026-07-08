# Cryptographic Failures (A02:2021)

Cryptographic Failures (formerly "Sensitive Data Exposure") is about sensitive data - passwords, credit cards, health records, tokens - being exposed because crypto is **missing, weak, or misused**. The failure is usually not "someone broke AES"; it's plaintext transport, bad hashing, hardcoded keys, or weak configuration.

## What counts as a cryptographic failure

- Sending sensitive data over **plaintext HTTP** (no TLS)
- Storing passwords with **fast/broken hashes** (MD5, SHA1, unsalted)
- Storing sensitive data **unencrypted** at rest
- **Hardcoded** keys/secrets in source or config
- Weak/deprecated algorithms (DES, RC4, ECB mode) or weak TLS (SSLv3, TLS 1.0)
- Predictable randomness (`rand()` instead of a CSPRNG) for tokens/keys
- Improper certificate validation (accepting any cert)

## How a pentester finds these

```bash
# 1. Is TLS present and strong? Check protocols, ciphers, cert
nmap --script ssl-enum-ciphers -p 443 target.tld
sslscan target.tld
testssl.sh https://target.tld

# 2. Sensitive data over HTTP or in URLs/logs?
#    Look in Burp history for tokens/PII in query strings, mixed content

# 3. Hunt for secrets in client code / repos
grep -riE "api[_-]?key|secret|password|BEGIN RSA" .
trufflehog git https://github.com/org/repo

# 4. Identify weak password hashes if you obtain a dump, then crack
hashid '5f4dcc3b5aa765d61d8327deb882cf99'   # identify (this is md5 of "password")
hashcat -m 0 hashes.txt rockyou.txt         # 0 = MD5, 100 = SHA1, 3200 = bcrypt
```

## Common real findings

- Login form posts over `http://` -> credentials sniffable
- Password DB uses `md5(password)` -> instantly cracked from rainbow tables
- JWT signed with a weak/guessable secret -> forge tokens (`hashcat -m 16500`)
- AES in ECB mode -> identical plaintext blocks leak (the "ECB penguin")
- Session token = `base64(userid:timestamp)` -> forgeable

## Mitigation - the fix

- **TLS everywhere** (TLS 1.2/1.3), HSTS, no mixed content, valid certs.
- Hash passwords with a **slow, salted** algorithm: **bcrypt / scrypt / Argon2**:

```python
# SAFE password hashing
import bcrypt
hashed = bcrypt.hashpw(password.encode(), bcrypt.gensalt())
```

- Encrypt sensitive data at rest with **AES-256-GCM** (authenticated) - never ECB.
- Manage keys in a **secrets manager / KMS / Vault**; never hardcode.
- Use a **CSPRNG** (`secrets` in Python, `crypto.randomBytes` in Node) for tokens/keys.
- Don't invent crypto; use vetted libraries and modern defaults.

## Practice

- [CryptoHack](https://cryptohack.org/), PortSwigger JWT labs, [testssl.sh](https://testssl.sh/)

## Reference

- [OWASP A02:2021 Cryptographic Failures](https://owasp.org/Top10/A02_2021-Cryptographic_Failures/)
