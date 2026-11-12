# OSWE (Offensive Security Web Expert)

OSWE is OffSec's advanced **white-box web application** cert. Unlike OSCP, you get source code and must find and chain vulnerabilities by reading the application, then write your own exploit scripts. It is the benchmark for serious web app and secure-code-review work.

## Course and exam

- Course: **WEB-300** (Advanced Web Attacks and Exploitation)
- Exam: **47 hours 45 minutes** hands-on, then 24 hours to submit the report
- Goal: achieve authentication bypass and remote code execution on the targets, with working exploit code

## What it covers

- Source-code review to spot logic and injection flaws
- Chaining bugs into auth bypass and RCE
- Blind/second-order SQL injection and deserialization
- Writing clean, reusable exploit scripts (usually Python)
- Working across languages (PHP, Java, C#, Node.js, etc.)

## Prerequisites

- Comfortable reading code and debugging
- Solid grasp of the [OWASP Top 10](../OWASP%20Top%2010/OWASP%20Top%2010.md)
- Scripting ability in Python (see [Scripting for Pentesters](../Scripting/Python%20for%20Pentesters.md))

## How to prepare

- WEB-300 labs end to end
- PortSwigger [Web Security Academy](../Labs/Web%20Security%20Academy.md) advanced labs
- Read real code: vulnerable-by-design apps and public CVE write-ups

## Resources

- [OffSec WEB-300 / OSWE](https://www.offsec.com/courses/web-300/)

## Compare

- **BSCP** - cheaper, black-box web practitioner cert
- **OSWE** - white-box, exploit-dev-flavored web expert
- **OSCP+** - broad network/AD, less web depth
