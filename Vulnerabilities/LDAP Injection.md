# What is LDAP Injection?

LDAP Injection is the directory-service cousin of SQL injection. When an app builds an LDAP search filter from user input without escaping it, an attacker can rewrite the filter to bypass authentication, enumerate the directory, or read attributes they should not see. It commonly shows up in login forms and address-book search backed by Active Directory or OpenLDAP.

## How it works

- LDAP filters use a prefix syntax like `(&(uid=alice)(userPassword=secret))`
- Special characters `( ) * \ | &` control filter logic
- Unescaped input lets you inject those characters and change the query

## Test payloads

```text
# Auth bypass - always-true filter
*
*)(uid=*))(|(uid=*
admin)(&))

# Wildcards for enumeration
*)(uid=*
a*

# Blind attribute extraction (character by character)
*)(userPassword=a*
*)(userPassword=b*
```

For a login that builds `(&(uid=USER)(userPassword=PASS))`, sending user `*)(uid=*))(|(uid=*` and any password can collapse the password check.

## Tools

- [Burp Suite Intruder](https://portswigger.net/burp) - brute-force blind extraction
- [ldapsearch](https://linux.die.net/man/1/ldapsearch) - validate filter behavior directly
- [ldap-brute (nmap NSE)](https://nmap.org/nsedoc/scripts/ldap-brute.html)

## Manual testing

1. Inject `*` into any field that feeds an LDAP search and watch results grow
2. Add `)` and `(` to test whether you can break out of the filter
3. Craft an always-true condition to bypass a login
4. For blind cases, extract attributes one character at a time with wildcards

## Mitigation

- Escape all special characters per RFC 4515 before building filters
- Use the LDAP library's parameterized/encoded filter builders
- Bind the app with a least-privilege service account
- Validate input against an allowlist (usernames are not `*`)

## Deep dive

- [LDAP Injection - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [OWASP LDAP Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/LDAP_Injection_Prevention_Cheat_Sheet.html)

## CWE

- CWE-90: Improper Neutralization of Special Elements used in an LDAP Query
