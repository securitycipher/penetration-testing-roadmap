# Nuclei

Nuclei is a template-based vulnerability scanner from ProjectDiscovery. It runs 5000+ community templates for CVEs, misconfigs, and exposed panels.

## When to use it

- After recon identifies live hosts (pair with httpx)
- Quick CVE checks across many subdomains
- CI/CD pipeline security gates
- Not a replacement for manual testing - high false positive rate on some templates

## Quick start

```bash
# Scan single target
nuclei -u https://target.com

# Scan list of hosts
cat live_hosts.txt | nuclei -silent -severity critical,high

# Specific tags
nuclei -u https://target.com -tags cve,exposure,tech

# Update templates
nuclei -update-templates
```

## Template categories

- CVE-specific checks (Log4j, Spring4Shell, etc.)
- Exposed panels (Jenkins, Grafana, Kibana)
- Misconfigurations (open redirects, CORS, missing headers)
- Takeover checks (subdomain, S3 bucket)

## The full recon -> scan pipeline

```bash
# Discover subdomains, keep only the live ones, then scan them all
subfinder -d target.tld -silent \
  | httpx -silent \
  | nuclei -severity critical,high,medium -o findings.txt
```

## Useful flags

```bash
nuclei -l hosts.txt -rl 50 -c 25          # rate limit 50 req/s, 25 concurrency
nuclei -u https://target.tld -t custom/   # your own template directory
nuclei -u https://target.tld -id CVE-2021-44228   # run one specific template
nuclei -u https://target.tld -json -o out.json    # machine-readable output
nuclei -u https://target.tld -proxy http://127.0.0.1:8080   # route via Burp
```

## Writing a custom template (YAML)

```yaml
id: example-exposed-file
info:
  name: Exposed .env file
  severity: high
http:
  - method: GET
    path:
      - "{{BaseURL}}/.env"
    matchers:
      - type: word
        words:
          - "DB_PASSWORD"
```

## Notes

- Great for breadth (many hosts, known issues); **not** a substitute for manual testing.
- Some templates are noisy/false-positive prone - verify criticals by hand.
- Keep templates updated (`nuclei -update-templates`) before every engagement.

## Resources

- [Nuclei GitHub](https://github.com/projectdiscovery/nuclei)
- [Nuclei templates](https://github.com/projectdiscovery/nuclei-templates)

## Related tools

- [subfinder](https://github.com/projectdiscovery/subfinder) + [httpx](https://github.com/projectdiscovery/httpx) - recon pipeline before Nuclei
