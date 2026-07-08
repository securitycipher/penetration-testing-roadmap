# OSINT Basics

Open-source intelligence (OSINT) is gathering information from public sources before you touch the target network. Good OSINT saves hours of blind scanning.

## What to collect

- Subdomains and historical DNS
- Employee names, emails, job titles (for phishing scope awareness)
- GitHub/GitLab repos, leaked API keys, internal hostnames in JS
- Technology stack (Wappalyzer, BuiltWith, response headers)
- Certificate transparency logs (crt.sh)

## Tools

| Tool | Use |
|------|-----|
| [crt.sh](https://crt.sh) | Certificate transparency subdomain discovery |
| [Shodan](https://shodan.io) | Internet-wide service search |
| [theHarvester](https://github.com/laramies/theHarvester) | Email and subdomain harvesting |
| [Amass](https://github.com/owasp-amass/amass) | Deep subdomain enumeration |
| [TruffleHog](https://github.com/trufflesecurity/trufflehog) | Secret scanning in repos |

## Quick workflow

```bash
# Passive subdomains via crt.sh
curl -s "https://crt.sh/?q=%25.target.com&output=json" | jq -r '.[].name_value' | sort -u

# theHarvester (emails, subs, hosts from many sources)
theHarvester -d target.com -b crtsh,google,bing,linkedin

# WHOIS + DNS records
whois target.com
dig target.com ANY +noall +answer
dnsrecon -d target.com

# Check for exposed .git / secrets
ffuf -u https://target.com/FUZZ -w git-wordlist.txt -mc 200
```

## Google dorking (find exposed data)

```text
site:target.com filetype:pdf              # documents
site:target.com inurl:admin | inurl:login # panels
site:target.com intitle:"index of"        # directory listings
site:target.com ext:sql | ext:log | ext:env
"target.com" site:pastebin.com            # leaked data
site:github.com "target.com" password     # leaked secrets
```

## GitHub / secret recon

```bash
# Search org repos for secrets
trufflehog github --org=target-org
gitleaks detect --source=./repo

# Shodan queries (browser or CLI)
shodan search hostname:target.com
shodan search org:"Target Inc" port:3389
```

## Passive vs active

- **Passive** (crt.sh, Shodan, WHOIS, GitHub) - no packets to the target, undetectable.
- **Active** (dig against their DNS, port scan, ffuf) - touches the target, may be logged.
Do passive first to stay quiet and build a picture.

## Practice

- [TryHackMe - OSINT](https://tryhackme.com)
- [HackTheBox - OSINT challenges](https://www.hackthebox.com)

## Related

- [Penetration Testing Tricks](https://securitycipher.com/penetration-testing-tricks/)
