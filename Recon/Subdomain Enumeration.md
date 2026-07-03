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

## What to do with results

1. Probe live hosts for different tech stacks (staging often runs older code)
2. Check for subdomain takeover (dangling CNAME to deleted S3/CloudFront/Heroku)
3. Crawl each host for unique endpoints and JS files
4. Compare against program scope before testing

## Tools

- [subfinder](https://github.com/projectdiscovery/subfinder)
- [httpx](https://github.com/projectdiscovery/httpx)
- [nuclei](https://github.com/projectdiscovery/nuclei) - takeover templates

## Deep dive

- [Subdomain Takeover - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
