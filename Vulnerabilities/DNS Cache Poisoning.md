# What is DNS Cache Poisoning?

DNS Cache Poisoning (DNS spoofing) is when an attacker injects a forged DNS record into a resolver's cache, so users who ask for `bank.tld` get sent to the attacker's IP. Because the poison sits in the cache, every downstream client is affected until the record expires. It underpins phishing, traffic interception, and mass redirection.

## How it works

1. A client asks the resolver for `bank.tld`.
2. The resolver has no cached answer, so it queries the authoritative name server and **waits**.
3. The response is matched only by: **transaction ID (16-bit)**, **source/destination ports**, and the question.
4. The attacker floods forged responses guessing the transaction ID. If a forged packet arrives **before** the real one and matches, it wins - and gets **cached** (poisoned) for the whole TTL.

Only 16 bits of transaction ID + a UDP port to guess is why the classic attack was feasible - the **Kaminsky** technique made it reliable by querying many non-existent subdomains to get fresh races.

## Impact

- Redirect `bank.tld` (or software update servers) to attacker IPs -> phishing, malware
- Intercept email (poison MX), TLS-strip, mass redirection of every client using that resolver

## Lab commands

```bash
# See what a resolver currently returns
dig @RESOLVER_IP bank.tld A +short
nslookup bank.tld RESOLVER_IP

# Watch transaction IDs and source-port randomness on the wire
tcpdump -n -i eth0 udp port 53

# Is the resolver randomising source ports? (Kaminsky resistance test)
dig +short porttest.dns-oarc.net TXT @RESOLVER_IP

# Is DNSSEC validating? (should FAIL for a bad sig domain)
dig @RESOLVER_IP dnssec-failed.org +dnssec
```

Kaminsky angle: force queries for many random subnames (`aaaa1.bank.tld`, `aaaa2.bank.tld`, ...) so each miss triggers a new outbound query you can race, and inject a forged NS/glue record in the Authority/Additional sections.

## Tools

- [scapy](https://scapy.net/) - craft and race forged DNS responses in a lab
- [dnschef](https://github.com/iphelix/dnschef) - DNS proxy for spoofing tests
- [Wireshark](https://www.wireshark.org/) - analyse query IDs and timing
- [Bettercap](https://www.bettercap.org/) - local-network DNS spoofing (MITM)

## Related: local DNS spoofing (MITM)

On a LAN you control (authorised), you don't need to win the ID race - just answer faster via ARP spoofing:

```bash
bettercap -iface eth0 -eval "set arp.spoof.targets 192.168.1.10; arp.spoof on; set dns.spoof.domains bank.tld; set dns.spoof.address 10.0.0.5; dns.spoof on"
```

## Mitigation - the fix

- Deploy **DNSSEC** so forged records fail signature validation.
- Randomise **source ports** and **transaction IDs** (Kaminsky mitigation).
- Use **0x20 (case) encoding** for extra entropy; enforce short, sane TTLs.
- Prefer **DNS over TLS/HTTPS (DoT/DoH)** between clients and resolvers.

## Practice

- Build a lab with BIND + scapy; TryHackMe networking rooms

## Deep dive

- [DNS Cache Poisoning - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [Cloudflare: DNS cache poisoning](https://www.cloudflare.com/learning/dns/dns-cache-poisoning/)

## CWE

- CWE-350: Reliance on Reverse DNS Resolution for a Security-Critical Action
- CWE-345: Insufficient Verification of Data Authenticity
