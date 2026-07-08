# OWASP ZAP (Zed Attack Proxy)

ZAP is a **free, open-source web app security scanner and intercepting proxy** maintained by the OWASP community. It's the main open-source alternative to Burp Suite - strong at automated scanning and great for CI/CD because it's fully scriptable and headless-capable.

## ZAP vs Burp (quick take)

- **ZAP** - free, full automated active scanner included, excellent for CI/CD automation
- **Burp** - richer manual tooling and extensions; scanner requires paid Pro
- Many testers use ZAP for automation and Burp for manual work.

## Getting started (GUI)

```text
1. Launch ZAP -> choose to persist the session or not.
2. Use the built-in browser (ZAP > Manual Explore) - proxy + cert pre-configured.
3. Browse the target so ZAP builds the Sites tree.
4. Right-click the site > Attack > Spider (crawl) then Active Scan.
5. Review findings in the Alerts tab (grouped by risk).
```

## Scan modes

- **Passive scan** - always on; analyzes traffic you browse without sending attacks (safe).
- **Spider** - classic link crawler; **AJAX Spider** drives a real browser for JS-heavy apps.
- **Active scan** - sends attack payloads (SQLi, XSS, etc.) - only against authorized targets.

## Automation (the real strength) - CLI / headless

```bash
# Baseline scan (passive, quick) - great for CI pipelines
zap-baseline.py -t https://target.tld -r report.html

# Full active scan
zap-full-scan.py -t https://target.tld -r report.html

# API-driven scan via Docker
docker run -t ghcr.io/zaproxy/zaproxy zap-baseline.py -t https://target.tld
```

Automation Framework (YAML plan) lets you define spider + active scan + auth + reporting as code, ideal for repeatable CI/CD gates.

## Useful features

- **HUD (Heads-Up Display)** - overlays ZAP controls directly in the browser.
- **Fuzzer** - payload-based fuzzing of parameters (like Burp Intruder).
- **Requester/Manual Request Editor** - resend/modify requests (like Burp Repeater).
- **Auth handling** - form/JSON/script-based login for authenticated scans.
- **Add-on Marketplace** - extend with community add-ons.

## Tips

- Set the **context and scope** before active scanning to avoid hitting out-of-scope hosts.
- Configure **authentication + session management** so the scanner tests logged-in areas.
- Never run an active scan on systems you're not authorized to test.

## Resources

- [ZAP documentation](https://www.zaproxy.org/docs/)
- [ZAP Automation Framework](https://www.zaproxy.org/docs/automate/)
