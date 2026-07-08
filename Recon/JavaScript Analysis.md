# JavaScript Analysis

Modern web apps ship half their attack surface in JavaScript bundles. Static analysis of JS files finds hidden API routes, internal URLs, and hardcoded secrets.

## What to look for

- API base URLs and endpoint paths (`/api/v1/`, `/internal/`)
- Hardcoded API keys, AWS keys, tokens (search for `AKIA`, `api_key`, `secret`)
- GraphQL endpoints and introspection
- Source maps (`.map` files) that reveal unminified source
- WebSocket URLs and admin paths

## Collect all JS, then mine it

```bash
# 1. Gather JS URLs from crawl + archives
gau target.com | grep '\.js$' | httpx -silent -mc 200 -o js_urls.txt
katana -u https://target.com -jc | grep '\.js$' >> js_urls.txt

# 2. Download them all
mkdir js && cd js && wget -i ../js_urls.txt

# 3. Extract endpoints
cat *.js | grep -oE '"(/[a-zA-Z0-9_/?.=&-]+)"' | sort -u
xnLinkFinder -i ./js/ -o endpoints.txt

# 4. Extract URLs
cat *.js | grep -oE 'https?://[^"'\'' ]+' | sort -u
```

## Secret-hunting regexes

```bash
# Common secret patterns
grep -rEo 'AKIA[0-9A-Z]{16}' ./js/            # AWS access key
grep -rEo 'AIza[0-9A-Za-z_-]{35}' ./js/       # Google API key
grep -rEi '(api[_-]?key|secret|token|password)\s*[:=]\s*["'\''][^"'\'']+' ./js/

# Automated
trufflehog filesystem ./js/
```

## Source maps = free source code

If a `.map` file exists, you can reconstruct the original unminified source:

```bash
curl -s https://target.com/static/app.js.map -o app.js.map
npx source-map-explorer app.js app.js.map     # or unwebpack-sourcemap to rebuild files
```

| Tool | Purpose |
|------|---------|
| [LinkFinder](https://github.com/GerbenJavado/LinkFinder) | Extract endpoints from JS |
| [xnLinkFinder](https://github.com/xnl-h4ck3r/xnLinkFinder) | Modern LinkFinder fork |
| [Burp Suite](https://portswigger.net/burp) | Passive JS analysis in proxy history |
| [retire.js](https://retirejs.github.io/retire.js/) | Vulnerable JS library detection |

## Practice

- Recon rooms on TryHackMe and HTB
- Bug bounty programs with `*.target.com` scope

## Related

- [API Security Testing](API%20Security/REST%20API%20Testing.md)
