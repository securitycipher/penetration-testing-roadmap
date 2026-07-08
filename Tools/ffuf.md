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

## Matchers and filters (cut the noise)

```bash
-mc 200,301,403     # match status codes         -fc 404      filter status codes
-ms 1234            # match response size         -fs 0        filter by size
-ml 50              # match line count            -fl 10       filter by lines
-mw 20              # match word count            -fw 100      filter by words
-mr "admin"         # match regex in response     -fr "error"  filter regex
```

Tip: run once, see the size of the "not found" page, then `-fs <that-size>` to hide it.

## More real commands

```bash
# Recursive directory discovery
ffuf -u https://target.tld/FUZZ -w dirs.txt -recursion -recursion-depth 2 -mc 200,301

# POST body / login brute (cluster style with two lists)
ffuf -u https://target.tld/login -X POST \
  -d "user=admin&pass=FUZZ" -H "Content-Type: application/x-www-form-urlencoded" \
  -w rockyou.txt -fc 401

# Add extensions to each word
ffuf -u https://target.tld/FUZZ -w words.txt -e .php,.bak,.txt

# Auth + rate limit + save JSON
ffuf -u https://target.tld/FUZZ -w dirs.txt -H "Cookie: session=..." \
  -rate 50 -o out.json -of json
```

## Why pentesters use it

- Faster than gobuster/dirb for large wordlists
- Flexible FUZZ placement in URL, headers, and body
- Powerful matcher/filter engine; JSON output for piping

## Wordlists

- [SecLists](https://github.com/danielmiessler/SecLists) - `Discovery/Web-Content/` is the go-to

## Resources

- [ffuf GitHub](https://github.com/ffuf/ffuf)
