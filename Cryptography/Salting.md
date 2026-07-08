# Salting
## What is Salting?

Salting is a technique used to enhance the security of stored passwords by adding random data to the passwords before hashing them. This process helps protect against common attacks like rainbow table attacks and makes it more challenging for attackers to use precomputed tables of hashed passwords.

## Why is Salting Important?

When a system stores passwords, it should never store them in plaintext due to the potential security risks. Instead, systems typically use a process called hashing, where the password is transformed into a fixed-length string of characters that looks random. However, this process has a weakness - if two users have the same password, their hashed passwords will also be the same. This is where salting comes in.

## How Does Salting Work?

When a user creates an account or changes their password, a unique random value (the salt) is generated for that user. The salt is then combined with the password, and the result is hashed. The hashed password and the salt are stored in the system's database.

For example, let's say a user chooses the password "password123" and a unique salt "a1b2c3d4" is generated for them. The system would store the hash of "password123a1b2c3d4" along with the salt "a1b2c3d4."

## Benefits of Salting:

- Uniqueness: Each user has a unique salt, even if they have the same password. This prevents attackers from recognizing patterns and using precomputed tables effectively.

- Security: Salting increases the complexity for attackers attempting to crack passwords. Even if they have a precomputed table for common passwords, they would need to generate a new table for each unique salt.

- Randomness: The use of random salts ensures that even users with the same password will have different hash representations, adding an extra layer of unpredictability.

- Protection Against Rainbow Tables: Rainbow tables are precomputed tables of hashed passwords. Salting makes these tables ineffective because each salt requires a separate table.

## Example of Salting in Code:
In this example, the hash_password function takes a password and an optional salt. If no salt is provided, a random salt is generated using os.urandom(). The password and salt are then combined, and the hashlib.pbkdf2_hmac function is used to perform a secure hash.

```python
import hashlib
import os

def hash_password(password, salt=None):
    if salt is None:
        salt = os.urandom(16)  # Generate a random 16-byte salt
    
    # Combine the password and salt, then hash the result
    hashed_password = hashlib.pbkdf2_hmac('sha256', password.encode('utf-8'), salt, 100000)
    
    return hashed_password, salt

# Example usage
password = "password123"
hashed_password, salt = hash_password(password)

print(f"Password: {password}")
print(f"Hashed Password: {hashed_password}")
print(f"Salt: {salt}")

```
Salting is a crucial step in securing password storage and plays a significant role in protecting user accounts from various attacks. It adds complexity, randomness, and uniqueness to the hashed passwords, making it more challenging for attackers to compromise user accounts.

---

## Salting from a pentester's view

When you dump password hashes, the presence and quality of salting determines how you attack them.

### Spotting missing / weak salting

```bash
# Unsalted hashes: identical passwords -> identical hashes.
# If two users share a hash value, the password is the same AND unsalted.
sort hashes.txt | uniq -d      # duplicate hashes = no per-user salt
```

### Impact of no salt

- **Rainbow tables work** — precomputed hash→password lookups crack instantly.

```bash
# Example: crack an unsalted MD5 instantly via lookup or fast GPU
hashcat -m 0 -a 0 unsalted.txt rockyou.txt
```

### Impact of salt (done right)

- Rainbow tables become useless (a table per salt is infeasible).
- You must brute-force **each hash individually**, which is why a **slow salted algorithm (bcrypt/Argon2)** is the goal — see [Hashing](Hashing.md).

### Common findings to report

- Passwords hashed with fast algorithms (MD5/SHA-256) even *with* a salt — still crackable fast.
- A **global/shared salt** (same salt for every user) instead of per-user random salts.
- **Pepper** (a secret salt kept outside the DB) missing — a defense-in-depth gap.

### The correct pattern

```python
# bcrypt handles per-user salt generation automatically
import bcrypt
hashed = bcrypt.hashpw(password.encode(), bcrypt.gensalt(rounds=12))
bcrypt.checkpw(password.encode(), hashed)
```

## Related

- [Hashing](Hashing.md)
- [Cryptographic Failures](../OWASP%20Top%2010/Cryptographic%20Failures.md)
