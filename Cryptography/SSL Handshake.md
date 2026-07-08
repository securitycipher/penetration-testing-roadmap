# SSL Handshake
The SSL handshake is a crucial process that occurs when two parties, typically a web browser and a server, establish a secure communication channel over the internet. SSL stands for Secure Socket Layer, and it has been succeeded by the more modern Transport Layer Security (TLS). However, for simplicity, I'll refer to it as SSL throughout this explanation.

Here's a simplified breakdown of the SSL handshake:

- Client Hello:
  - The process begins when a client (e.g., your web browser) initiates a connection to a server. The client sends a "Hello" message to the server, indicating that it wants to establish a secure connection.
  - This message includes information like the SSL/TLS version the client supports, supported cipher suites (encryption algorithms), and other parameters.

- Server Hello:
  - The server responds with its own "Hello" message, selecting the highest SSL/TLS version that both the client and server support.
  - The server also chooses a cipher suite from the list provided by the client. The cipher suite includes encryption algorithms for securing the data transmission.
- Key Exchange:

  - The server sends its public key to the client. The public key is a part of the server's SSL certificate, which also includes information about the server's identity.
  - The client verifies the server's SSL certificate to ensure it is valid, issued by a trusted Certificate Authority (CA), and belongs to the server it claims to represent.
- Pre-master Secret:

  - The client generates a random pre-master secret and encrypts it with the server's public key.
  - This encrypted pre-master secret is then sent back to the server.
- Session Key Derivation:

  - Both the client and server independently derive a session key from the pre-master secret and other parameters exchanged during the handshake.
  - This session key will be used for encrypting and decrypting the actual data exchanged during the secure session.
- Finished:

  - The client sends a "Finished" message to the server to signal the completion of its part of the handshake.
  - The server also sends its "Finished" message to the client.

At this point, the SSL handshake is complete, and a secure connection has been established. From this moment on, the client and server can exchange information securely, as the data will be encrypted using the derived session key. This encryption ensures confidentiality and integrity, preventing unauthorized parties from intercepting or tampering with the transmitted data. The SSL handshake is a fundamental process that underlies secure communication on the internet, providing a foundation for secure transactions, data exchange, and confidentiality.

---

## Testing TLS/SSL as a pentester

TLS config is one of the most commonly assessed items — automated and quick, with clear pass/fail results.

### Scan the configuration

```bash
# Comprehensive one-shot TLS audit (protocols, ciphers, cert, known CVEs)
testssl.sh https://target.com

# Fast cipher/protocol enumeration
sslscan target.com:443
nmap --script ssl-enum-ciphers -p 443 target.com

# Inspect the certificate directly
openssl s_client -connect target.com:443 -servername target.com < /dev/null | openssl x509 -noout -text
```

### What counts as a finding

- **Deprecated protocols enabled** — SSLv2, SSLv3, TLS 1.0/1.1.
- **Weak ciphers** — RC4, 3DES, NULL, EXPORT, anything without forward secrecy.
- **Certificate issues** — expired, self-signed, wrong hostname, weak key (<2048-bit RSA), signed with SHA-1.
- **Known protocol CVEs** — Heartbleed, POODLE, BEAST, CRIME, ROBOT, DROWN.
- **Missing HSTS** — allows SSL-strip downgrade.

### MITM / interception (with a trusted CA)

```bash
# Intercept HTTPS in Burp/ZAP by installing their CA cert on the client.
# If the app pins certs (common in mobile), you must bypass pinning:
objection -g com.target.app explore    # then: android sslpinning disable
```

### The handshake is where downgrade attacks live

Because the client and server negotiate protocol + cipher in the Hello messages, a MITM can strip or force weaker options if the server permits them. Enforcing TLS 1.2+ with strong ciphers and HSTS closes these gaps.

## Related

- [Cryptographic Failures](../OWASP%20Top%2010/Cryptographic%20Failures.md)
- [Certificate Authority (CA)](Certificate%20Authority%20(CA).md) · [PKI](Public%20Key%20Infrastructure%20(PKI).md)
