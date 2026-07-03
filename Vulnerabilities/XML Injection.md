# What is XML Injection?

XML Injection happens when user input is written into an XML document or message without encoding, letting an attacker change the document's structure. Depending on how the XML is used, this can mean tampering with data, bypassing logic, or opening the door to XXE and XPath injection. It is common in SOAP services, config files, and any API that builds XML from strings.

## How it works

- The app inserts raw input between XML tags or into attributes
- Injecting `<`, `>`, or `"` lets you close tags early and add new nodes
- New nodes can overwrite trusted values (for example, a role or price field)

## Test payloads

```xml
<!-- Probe: does the parser choke on a stray tag? -->
test</username><username>injected

<!-- Overwrite a trusted field by injecting a later, duplicate node -->
<user>
  <name>attacker</name>
  <role>user</role><role>admin</role>
</user>

<!-- CDATA to smuggle characters past naive filters -->
<![CDATA[<script>alert(1)</script>]]>
```

```text
# XPath injection often rides along with XML Injection
' or '1'='1
']/parent::*/child::node()[1]
```

## Tools

- [Burp Suite](https://portswigger.net/burp) - Repeater to reshape XML by hand
- [xcat](https://github.com/orf/xcat) - automated XPath injection exploitation
- [SoapUI](https://www.soapui.org/) - craft and replay SOAP requests

## Manual testing

1. Submit input containing `<`, `>`, and `"` and check whether the XML still parses
2. Try to close an existing element and open a new one
3. Inject a duplicate node for a security-relevant field (role, amount) and see which wins
4. If the XML feeds an XPath query, test XPath injection strings too

## Mitigation

- Build XML with a library/DOM API, never string concatenation
- Encode user data for XML context (`&lt; &gt; &amp; &quot; &apos;`)
- Validate against a strict XSD schema and reject unexpected nodes
- Disable DTD processing to prevent XXE riding on the same input

## Deep dive

- [XML Injection - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [OWASP XML Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/XML_Security_Cheat_Sheet.html)

## CWE

- CWE-91: XML Injection (aka Blind XPath Injection)
