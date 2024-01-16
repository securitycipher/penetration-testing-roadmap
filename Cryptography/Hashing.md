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
