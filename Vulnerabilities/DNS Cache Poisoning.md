# What is DNS Cache Poisoning?

DNS Cache Poisoning (DNS spoofing) is when an attacker injects a forged DNS record into a resolver's cache, so users who ask for `bank.tld` get sent to the attacker's IP. Because the poison sits in the cache, every downstream client is affected until the record expires. It underpins phishing, traffic interception, and mass redirection.

## How it works

- A resolver queries an authoritative server and waits for the answer
- The attacker races to send a forged response with a matching query ID and source port
- If it arrives first and matches, the resolver caches the malicious record

## Test / lab commands

```bash
# Inspect what a resolver currently returns
dig @RESOLVER_IP bank.tld A +short
nslookup bank.tld RESOLVER_IP

# Watch the transaction IDs and source port randomness
tcpdump -n -i eth0 udp port 53

# Check whether a resolver randomizes source ports (Kaminsky resistance)
dig +short porttest.dns-oarc.net TXT @RESOLVER_IP
```

```text
# Classic Kaminsky angle: force queries for many random subnames
# (aaaa1.bank.tld, aaaa2.bank.tld ...) to widen the spoofing window
```

## Tools

- [scapy](https://scapy.net/) - craft and race forged DNS responses in a lab
- [dnschef](https://github.com/iphelix/dnschef) - DNS proxy for spoofing tests
- [Wireshark](https://www.wireshark.org/) - analyze query IDs and timing

## Manual testing

1. Confirm the resolver randomizes both transaction ID and source port
2. In a controlled lab, attempt to win the race with forged responses
3. Verify whether the target validates DNSSEC signatures
4. Check TTLs - long TTLs make a successful poison last longer

## Mitigation

- Deploy DNSSEC so forged records fail signature validation
- Randomize source ports and query IDs (Kaminsky mitigation)
- Use 0x20 encoding and enforce short, sane TTLs
- Prefer encrypted transport (DNS over TLS/HTTPS) between clients and resolvers

## Deep dive

- [DNS Cache Poisoning - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [Cloudflare: DNS cache poisoning](https://www.cloudflare.com/learning/dns/dns-cache-poisoning/)

## CWE

- CWE-350: Reliance on Reverse DNS Resolution for a Security-Critical Action
- CWE-345: Insufficient Verification of Data Authenticity
