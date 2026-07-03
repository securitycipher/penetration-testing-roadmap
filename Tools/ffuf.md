# ffuf

ffuf (Fuzz Faster U Fool) is a fast web fuzzer written in Go. It is the default tool for directory, parameter, and vhost discovery in 2026 bug bounty workflows.

## Common use cases

- Directory and file discovery
- Virtual host (vhost) enumeration
- Parameter fuzzing (`?FUZZ=value`)
- POST body fuzzing
- Header fuzzing (Host, X-Forwarded-For)

## Quick commands

```bash
# Directory fuzz
ffuf -u https://target.com/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt -mc 200,301,302,403

# Vhost fuzz
ffuf -u https://target.com -H "Host: FUZZ.target.com" -w subdomains.txt -mc 200

# Parameter discovery
ffuf -u https://target.com/page?FUZZ=test -w params.txt -mc 200

# Filter by response size (hide common 404 page)
ffuf -u https://target.com/FUZZ -w wordlist.txt -mc 200 -fs 1234
```

## Why pentesters use it

- Faster than gobuster/dirb for large wordlists
- Flexible FUZZ placement in URL, headers, and body
- JSON output for piping into other tools

## Resources

- [ffuf GitHub](https://github.com/ffuf/ffuf)
- [Penetration Testing Tricks](https://securitycipher.com/penetration-testing-tricks/)
