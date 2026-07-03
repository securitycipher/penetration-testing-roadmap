# JavaScript Analysis

Modern web apps ship half their attack surface in JavaScript bundles. Static analysis of JS files finds hidden API routes, internal URLs, and hardcoded secrets.

## What to look for

- API base URLs and endpoint paths (`/api/v1/`, `/internal/`)
- Hardcoded API keys, AWS keys, tokens (search for `AKIA`, `api_key`, `secret`)
- GraphQL endpoints and introspection
- Source maps (`.map` files) that reveal unminified source
- WebSocket URLs and admin paths

## Tools

```bash
# Extract URLs from JS files
cat app.js | grep -oE 'https?://[^\"'\'' ]+' | sort -u

# Link discovery with gau + httpx
gau target.com | grep '\.js$' | httpx -silent -mc 200 -o js_files.txt

# Secret scanning
trufflehog filesystem ./downloaded-js/
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
