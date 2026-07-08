# Hashing

## What is Hashing?

Hashing is a process of converting input data (or a 'message') into a fixed-size string of characters, which is usually a sequence of numbers and letters. This output is commonly referred to as a "hash value" or simply a "hash." The idea is that no matter how large or small the input data is, the hash value always has a fixed length.

## Key Characteristics of Hashing:

- Fixed Output Size: Hash functions produce a fixed-size output, regardless of the size of the input data. For example, whether you hash a single letter or an entire book, the resulting hash will have a predetermined length.
- Deterministic: The same input will always produce the same hash value. If you hash a specific piece of data using a particular hash function, you'll get the same hash every time.
- Irreversibility (One-way): Hash functions are designed to be one-way, meaning it should be computationally infeasible to reverse the process and obtain the original input data from its hash value. This property is crucial for security.
- Collision Resistance: A good hash function minimizes the chance of two different inputs producing the same hash value. This is known as a collision. Collision resistance is vital to ensure the uniqueness of hash values.
## Common Use Cases of Hashing:

- Data Integrity: Hashing is commonly used to verify the integrity of data. By comparing the hash value of original data with the hash value calculated after transferring or storing the data, one can determine if the data has been altered.
- Password Storage: Instead of storing plain-text passwords, systems often store the hash of a password. During login, the entered password is hashed, and the hash is compared with the stored hash. This adds a layer of security as the actual password is not stored.
- Digital Signatures: Hashing is an essential component in creating digital signatures. A hash of the message is signed with a private key, and the recipient can use the corresponding public key to verify both the authenticity of the sender and the integrity of the message.
- Hash Tables: In computer science, hash functions are used in data structures like hash tables to quickly locate a data record given its search key.
## Examples of Hash Functions:

- MD5 (Message Digest Algorithm 5): MD5 produces a 128-bit hash value and was widely used in the past. However, it is now considered insecure due to vulnerabilities.
- SHA-256 (Secure Hash Algorithm 256-bit): Part of the SHA-2 family, SHA-256 produces a 256-bit hash and is commonly used for secure applications like blockchain.
-bcrypt: bcrypt is a cryptographic hash function specifically designed for password hashing. It incorporates a salt (random data) and a cost factor to slow down hashing and make it more secure.

In summary, hashing is a fundamental concept in computer science and cryptography, providing essential tools for ensuring data integrity, securing passwords, and enabling various applications across computing.

---

## Hashing from a pentester's view

Once you dump password hashes (from `/etc/shadow`, the SAM/NTDS.dit, or a database), your job is to identify and crack them.

### Identify the hash type

```bash
hashid '$2b$12$...'         # or
hash-identifier
# Common formats:
#   MD5      -> 32 hex chars
#   SHA-1    -> 40 hex chars
#   SHA-256  -> 64 hex chars
#   NTLM     -> 32 hex chars (Windows)
#   bcrypt   -> starts with $2a$/$2b$
```

### Crack with hashcat (GPU) or John

```bash
# hashcat mode (-m) examples
hashcat -m 0    -a 0 md5.txt    rockyou.txt        # MD5
hashcat -m 1000 -a 0 ntlm.txt   rockyou.txt        # NTLM
hashcat -m 1800 -a 0 sha512.txt rockyou.txt        # sha512crypt
hashcat -m 3200 -a 0 bcrypt.txt rockyou.txt        # bcrypt (slow!)
hashcat -m 5600 -a 0 netntlm.txt rockyou.txt       # NetNTLMv2 (from Responder)

# Rule-based attack for mutations
hashcat -m 0 -a 0 md5.txt rockyou.txt -r rules/best64.rule

# John the Ripper equivalent
john --format=nt --wordlist=rockyou.txt ntlm.txt
john --show ntlm.txt
```

### Why algorithm choice matters

- **MD5 / SHA-1** are fast → billions of guesses/sec on a GPU → cracked quickly. **Broken; flag them.**
- **Unsalted** hashes fall to precomputed **rainbow tables** (see [Salting](Salting.md)).
- **bcrypt / scrypt / Argon2 / PBKDF2** are *deliberately slow* → orders of magnitude harder to crack. These are the correct choice for passwords.

### Pass-the-Hash

For NTLM, you often don't even need to crack — you can authenticate with the hash directly:

```bash
crackmapexec smb target -u admin -H <NTLM-hash>
```

## Related

- [Salting](Salting.md)
- [Cryptographic Failures](../OWASP%20Top%2010/Cryptographic%20Failures.md)
