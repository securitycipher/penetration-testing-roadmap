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

# theHarvester
theHarvester -d target.com -b crtsh,google

# Check for exposed .git
ffuf -u https://target.com/FUZZ -w git-wordlist.txt -mc 200
```

## Practice

- [TryHackMe - OSINT](https://tryhackme.com)
- [HackTheBox - OSINT challenges](https://www.hackthebox.com)

## Related

- [Penetration Testing Tricks](https://securitycipher.com/penetration-testing-tricks/)
