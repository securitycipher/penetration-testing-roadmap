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

## Resources

- [Nuclei GitHub](https://github.com/projectdiscovery/nuclei)
- [Nuclei templates](https://github.com/projectdiscovery/nuclei-templates)

## Related tools

- [subfinder](https://github.com/projectdiscovery/subfinder) + [httpx](https://github.com/projectdiscovery/httpx) - recon pipeline before Nuclei
