# Security Misconfiguration (A05:2021)

Security Misconfiguration covers all the ways a system is insecure not because of a coding bug but because it was **set up wrong**: default credentials, unnecessary features enabled, verbose errors, missing security headers, open cloud storage, and outdated configs. In 2021 OWASP folded **XXE** into this category. It is extremely common because every layer (app, framework, web server, container, cloud) has its own config.

## Common misconfigurations to hunt

- **Default / weak credentials** (admin:admin on Tomcat, Jenkins, routers, databases)
- **Directory listing** enabled; exposed `.git/`, `.env`, backups, `phpinfo()`
- **Verbose error messages / stack traces** leaking paths, versions, queries
- **Default pages / sample apps** (Tomcat `/manager`, `/examples`)
- **Missing security headers** (CSP, HSTS, X-Content-Type-Options)
- **Open cloud storage** (public S3 buckets, blob containers)
- **Unnecessary open ports/services**; debug mode on in production
- **Permissive CORS** (`Access-Control-Allow-Origin: *` with credentials)

## Testing commands

```bash
# Fingerprint tech, headers, and known misconfigs
whatweb https://target.tld
nikto -h https://target.tld
nuclei -u https://target.tld -t http/misconfiguration/

# Find exposed sensitive files/dirs
ffuf -u https://target.tld/FUZZ -w wordlist.txt -mc 200
curl -s https://target.tld/.git/config
curl -s https://target.tld/.env
curl -s https://target.tld/server-status

# Check security headers
curl -sI https://target.tld

# Public S3 bucket
aws s3 ls s3://bucket-name --no-sign-request
curl https://bucket-name.s3.amazonaws.com/

# Default creds - try known combos on admin panels, DBs, Tomcat /manager
```

## Full walkthrough - exposed .git

1. Request `https://target.tld/.git/config` -> returns content (repo exposed).
2. Dump the whole repo: `git-dumper https://target.tld/.git/ ./out`.
3. Read the source, find hardcoded DB creds / API keys in the history.
4. Log in / access the API with those secrets.

## Tools

- [nuclei](https://github.com/projectdiscovery/nuclei) - templated misconfig/CVE scanning
- [nikto](https://github.com/sullo/nikto), [whatweb](https://github.com/urbanadventurer/WhatWeb)
- [git-dumper](https://github.com/arthaud/git-dumper), [ffuf](https://github.com/ffuf/ffuf)

## Mitigation - the fix

- **Harden by default**: remove sample apps, disable directory listing and debug mode.
- Change all **default credentials**; enforce least privilege.
- Return **generic error pages**; log details server-side only.
- Add **security headers** (CSP, HSTS, X-Frame-Options, X-Content-Type-Options).
- Lock down cloud storage; audit with CIS benchmarks and IaC scanning.
- Automate config checks in CI; keep everything patched.

## Practice

- [PortSwigger information disclosure / misconfig labs](https://portswigger.net/web-security)

## Reference

- [OWASP A05:2021 Security Misconfiguration](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/)
