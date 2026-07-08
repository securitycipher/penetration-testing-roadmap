# Encryption and Decryption
## Encryption
Imagine you're sending a top-secret message to your friend, and you don't want anyone else to understand it if they happen to intercept it. This is where encryption comes in. Encryption is like putting your message in a secret code that only you and your friend can understand.

In the digital world, this involves transforming your original message (plaintext) into an unreadable format (ciphertext) using a specific algorithm and a key. The algorithm is like a set of rules, and the key is the secret ingredient that makes the encryption unique. So, even if someone gets hold of the encrypted message, they won't be able to make sense of it without the key.

There are different types of encryption algorithms, such as symmetric and asymmetric encryption:

- Symmetric Encryption: In symmetric encryption, the same key is used for both encryption and decryption. It's like having a single key to lock and unlock a door. Both you and your friend need to have the same key to understand the message.

- Asymmetric Encryption: Asymmetric encryption involves a pair of keys - a public key and a private key. The public key is used to encrypt the message, and the private key is used to decrypt it. It's like having a lock and key system where the lock (public key) is accessible to everyone, but only the owner has the unique key (private key) to open it.

## Decryption
Now, let's talk about decryption, which is the process of turning the encrypted message back into its original form. It's like revealing the hidden meaning of the secret code.

In symmetric encryption, the recipient uses the same key that was used for encryption to decrypt the message. In asymmetric encryption, the recipient uses their private key to decrypt the message that was encrypted with their public key.

In summary, encryption is like putting your message in a secure envelope with a lock, and decryption is like using the right key to open that envelope and read the message. It's a crucial aspect of securing digital communication and information in today's interconnected world.

---

## Encryption from a pentester's view

You rarely break modern encryption math — you break how it's **implemented and configured**.

### Common real-world findings

- **Hardcoded keys** — AES key committed in source or shipped in a mobile app / binary.
- **ECB mode** — identical plaintext blocks produce identical ciphertext (the famous "ECB penguin"); reveals patterns.
- **Static/reused IV or nonce** — breaks CBC/CTR/GCM guarantees.
- **Padding oracle** — CBC decryption error differences let you decrypt/encrypt without the key.
- **Weak/legacy ciphers** — DES, 3DES, RC4, export-grade suites.
- **No integrity** — encryption without a MAC (use AES-GCM or encrypt-then-MAC).

### Tools and commands

```bash
# OpenSSL for manual crypto operations
echo "secret" | openssl enc -aes-256-cbc -a -k password
openssl enc -d -aes-256-cbc -a -k password -in cipher.txt

# Padding oracle exploitation
padbuster http://target/decrypt?data=BASE64 BASE64 16 -encoding 0

# Find hardcoded keys/secrets in code, apps, binaries
trufflehog filesystem ./src
strings app.apk | grep -iE 'key|secret|aes|password'

# Analyze a captured TLS/crypto stream
wireshark   # (Statistics -> follow stream)
```

### Symmetric vs asymmetric — quick recall

- **Symmetric (AES)** — fast, one shared key; used for bulk data.
- **Asymmetric (RSA/ECC)** — slow, key pair; used for key exchange and [Digital Signatures](Digital%20Signature.md).
- TLS uses asymmetric to exchange a symmetric session key (see [SSL Handshake](SSL%20Handshake.md)).

## Related

- [Cryptographic Failures](../OWASP%20Top%2010/Cryptographic%20Failures.md)
- [Hashing](Hashing.md) · [SSL Handshake](SSL%20Handshake.md)
