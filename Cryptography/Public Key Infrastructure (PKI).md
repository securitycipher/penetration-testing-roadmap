# Public Key Infrastructure (PKI)
## What is PKI?
Public Key Infrastructure (PKI) is a set of technologies, processes, and standards that work together to secure communication and data. It's like a digital security system that helps ensure the confidentiality, integrity, and authenticity of information exchanged over networks, such as the internet.

## Key Concepts:
- Public Key and Private Key:
  - In PKI, each user has a pair of cryptographic keys: a public key and a private key.
  - The public key is shared openly and is used to encrypt information.
  - The private key is kept secret and is used to decrypt the information encrypted with the corresponding public key.
- Digital Certificates:
  - A digital certificate is like a digital ID card that verifies the ownership of a public key.
  - It contains information about the key owner, the public key itself, and is signed by a trusted third party known as a Certificate Authority (CA).
- Certificate Authority (CA):
  - A CA is a trusted entity that issues digital certificates. It vouches for the association between a public key and an individual or entity.
  - Popular CAs include companies like VeriSign, DigiCert, and Let's Encrypt.
- Registration Authority (RA):
  - The RA is responsible for verifying the identity of individuals or entities before a certificate is issued by the CA.
- Digital Signature:
  - Digital signatures are like electronic fingerprints that ensure the authenticity and integrity of a message or document.
  - They are created using the private key and can be verified using the corresponding public key.

## How PKI Works:
- Key Pair Generation: Users generate a pair of public and private keys.
- Certificate Request: Users request a digital certificate from the CA, providing proof of identity to the RA.
- Certificate Issuance: The CA verifies the user's identity and issues a digital certificate containing the public key.
- Certificate Distribution: The digital certificate is distributed to the user, making it publicly available.
- Digital Signatures and Encryption: Users can now use their private key to create digital signatures and decrypt messages encrypted with their public key.

## Use Cases:
- Secure Communication: PKI secures communication by encrypting data, ensuring that only the intended recipient can decrypt and understand the information.
- Digital Signatures: It provides a way to verify the authenticity of digital documents, assuring that they have not been tampered with.
- Authentication: PKI helps in authenticating users and devices in online transactions and access control systems.

In summary, PKI is a system that uses keys, certificates, and trusted authorities to establish a secure and reliable digital communication environment. It plays a crucial role in safeguarding sensitive information in the digital world.

---

## PKI from a pentester's view

PKI ties together the other crypto topics: [CAs](Certificate%20Authority%20(CA).md), [certificates](SSL%20Handshake.md), and [signatures](Digital%20Signature.md). Attacks target the *weak links* in the trust chain, not the math.

### Where PKI breaks in the real world

- **Leaked private keys** — a private key in a repo, backup, or config lets an attacker impersonate the entity. Game over for that identity.
- **Weak validation (broken chain-of-trust)** — apps that don't validate the full cert chain, hostname, or revocation accept forged certs → MITM.
- **Improper revocation** — compromised certs still trusted because OCSP/CRL isn't checked.
- **Rogue/internal CA abuse** — a trusted internal CA (ADCS) can mint certs for any identity (see [Certificate Authority](Certificate%20Authority%20(CA).md), ESC1–ESC8).
- **Certificate pinning bypass** — needed to intercept hardened mobile apps.

### Commands

```bash
# Hunt for exposed private keys
trufflehog filesystem ./ | grep -i private
find / -name "*.pem" -o -name "*.key" 2>/dev/null

# Verify a chain and revocation
openssl verify -CAfile ca.pem server.pem
openssl s_client -connect target:443 -status < /dev/null   # OCSP stapling check

# Enterprise ADCS abuse (Windows PKI)
certipy find -u user@domain -p pass -dc-ip 10.0.0.1 -vulnerable
```

### Client authentication (mTLS)

Some APIs require a **client certificate** (mutual TLS). If you can obtain or forge one (e.g., via a weak internal CA), you gain access no password would grant.

## Related

- [Certificate Authority (CA)](Certificate%20Authority%20(CA).md) · [Digital Signature](Digital%20Signature.md) · [SSL Handshake](SSL%20Handshake.md)
- [Cryptographic Failures](../OWASP%20Top%2010/Cryptographic%20Failures.md)
