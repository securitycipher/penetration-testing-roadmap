# Digital Signature
## What is a Digital Signature?

A digital signature is like an electronic version of your handwritten signature, but it goes beyond just representing your identity. It's a way of ensuring the authenticity and integrity of digital information, such as documents or messages, in the online world.

## How Does it Work?

- Key Pair: Imagine having two keys: a public key and a private key. The public key is shared with everyone, while the private key is kept secret.
- Creating the Signature: When you want to sign a document or message, your private key is used to create a unique digital signature. This process involves complex mathematical algorithms that ensure the signature is practically impossible to forge.
- Verification with Public Key: The digital signature is then attached to the digital information. Anyone who wants to verify the authenticity of the information can use your public key to check the signature.
- Authenticity Check: If the signature matches with the information and can be decrypted using your public key, it confirms that the information hasn't been tampered with and that it indeed came from you.

## Why is it Important?

- Authentication: Digital signatures provide a way to verify the identity of the sender. If a document carries a valid digital signature, you can trust that it comes from the claimed sender.
- Integrity: Digital signatures ensure that the content of a document or message hasn't been altered since it was signed. If someone tries to modify the information, the signature won't match, indicating tampering.
- Non-repudiation: With a digital signature, the sender cannot later deny their involvement. Once a digital signature is applied, it serves as evidence that the sender approved the content.
- Secure Transactions: In the context of online transactions or sensitive communications, digital signatures enhance security by preventing unauthorized access and ensuring data integrity.

## Real-world Analogy:
Think of it like sealing an envelope with a unique wax stamp. If someone opens the envelope or tampers with the contents, the seal is broken, indicating that the letter may have been compromised. In the digital world, a digital signature serves a similar purpose.

In summary, a digital signature is a sophisticated way to ensure the authenticity and integrity of digital information, using a pair of keys to create and verify unique signatures. It's a crucial component in securing online transactions, communications, and data.

---

## Signatures from a pentester's view

Signature *verification* is where the bugs live. If an app checks a signature incorrectly, you can forge tokens and data.

### JWT signature attacks (the classic)

JWTs are signed tokens — flawed verification leads directly to auth bypass.

```bash
# 1. alg:none — strip the signature entirely
#    Change header to {"alg":"none"} and remove the signature; some libs accept it.

# 2. Algorithm confusion (RS256 -> HS256)
#    Sign with the server's PUBLIC key as the HMAC secret.

# 3. Weak HMAC secret — brute-force it offline
hashcat -m 16500 jwt.txt rockyou.txt

# Automated JWT attacks
python3 jwt_tool.py <token> -M at        # all tests
python3 jwt_tool.py <token> -X a         # alg:none exploit
```

### Other signature-verification failures

- **Golden SAML** — steal the ADFS token-signing key → forge SAML assertions for any user (see [Hybrid Cloud](../Cloud/Hybrid%20Cloud.md)).
- **Webhook signature not verified** — spoof payloads (payment/CI callbacks).
- **Software update signatures skipped** — supply malicious updates (see [Software and Data Integrity Failures](../OWASP%20Top%2010/Software%20and%20Data%20Integrity%20Failures.md)).
- **Missing non-repudiation** — actions can't be attributed, hindering IR.

### Key takeaway

A signature is only as strong as the code that verifies it. Always test: *does the server actually reject a bad/absent/differently-signed token?*

## Related

- [Identification and Authentication Failures](../OWASP%20Top%2010/Identification%20and%20Authentication%20Failures.md)
- [PKI](Public%20Key%20Infrastructure%20(PKI).md) · [Hashing](Hashing.md)
