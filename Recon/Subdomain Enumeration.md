# Subdomain Enumeration

Subdomains are where forgotten staging boxes and old APIs hide. Most bounty payouts start with an asset others missed.

## Passive sources

- Certificate transparency (crt.sh, Censys)
- DNS aggregators (SecurityTrails, VirusTotal)
- Search engines (`site:*.target.com`)
- GitHub code search for `target.com`

## Active tools

```bash
# subfinder + httpx pipeline
subfinder -d target.com -all -silent | httpx -silent -status-code -title -tech-detect -o live.txt

# amass (slower, deeper)
amass enum -passive -d target.com -o amass_passive.txt

# DNS brute force
puredns resolve wordlist.txt -r resolvers.txt | httpx -silent
```

## Permutation / alteration scanning (find more)

```bash
# Generate permutations of known subdomains (dev-api, api-staging, ...)
gotator -sub subs.txt -perm words.txt -depth 2 | puredns resolve -r resolvers.txt

# altdns style permutations
altdns -i subs.txt -o perms.txt -w words.txt -r -s resolved.txt
```

## Subdomain takeover check

A dangling CNAME (pointing to a deleted S3/GitHub Pages/Heroku app) can be claimed by you:

```bash
# Detect
subjack -w subs.txt -t 50 -ssl -o takeovers.txt
nuclei -l live.txt -t http/takeovers/
# Confirm manually: does the page show "NoSuchBucket" / "There isn't a GitHub Pages site here"?
```

## Full recon pipeline

```bash
subfinder -d target.com -all -silent > subs.txt
amass enum -passive -d target.com >> subs.txt
sort -u subs.txt -o subs.txt
puredns resolve subs.txt -r resolvers.txt | httpx -silent -sc -title -td -o live.txt
nuclei -l live.txt -severity critical,high
```

## What to do with results

1. Probe live hosts for different tech stacks (staging often runs older code).
2. Check for **subdomain takeover** (dangling CNAME).
3. Crawl each host for unique endpoints and JS files.
4. Compare against program scope before testing.

## Tools

- [subfinder](https://github.com/projectdiscovery/subfinder), [amass](https://github.com/owasp-amass/amass)
- [puredns](https://github.com/d3mondev/puredns) / [massdns](https://github.com/blechschmidt/massdns) - fast resolution/brute
- [httpx](https://github.com/projectdiscovery/httpx), [subjack](https://github.com/haccer/subjack), [nuclei](https://github.com/projectdiscovery/nuclei)

## Deep dive

- [Subdomain Takeover - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
