# Nmap (Network Mapper)

Nmap is the de-facto tool for **host discovery, port scanning, service/version detection, OS detection, and scripted vulnerability checks**. It's usually the first thing you run against a network target to learn what's alive and what's listening. It sends crafted packets and interprets the responses to build a map of the target.

## Install

```bash
sudo apt install nmap        # Debian/Kali
brew install nmap            # macOS
```

## The typical workflow

```bash
# 1. Fast discovery of what's alive on a subnet (no port scan)
nmap -sn 10.10.10.0/24

# 2. Fast full-port sweep to find open ports quickly
nmap -p- --min-rate 5000 -T4 10.10.10.5 -oN allports.txt

# 3. Deep scan ONLY the open ports (version + default scripts + OS)
nmap -p 22,80,443 -sC -sV -O 10.10.10.5 -oN deep.txt
```

## Host discovery

```bash
nmap -sn 10.10.10.0/24        # ping sweep (no ports)
nmap -Pn 10.10.10.5           # skip discovery, treat as up (host blocks ping)
nmap -PS22,80,443 target      # TCP SYN ping to specific ports
nmap -n target                # no DNS resolution (faster)
```

## Port scan types

```bash
nmap -sS target      # SYN "stealth" scan (default as root, fast)
nmap -sT target      # full TCP connect (no root needed)
nmap -sU target      # UDP scan (slow but finds DNS/SNMP/etc.)
nmap -sU -sS target  # UDP + TCP together
nmap -p-             # all 65535 ports
nmap -p 80,443       # specific ports
nmap -F              # fast: top 100 ports
nmap --top-ports 1000
```

## Service, version, OS detection

```bash
nmap -sV target                 # service/version detection
nmap -sV --version-intensity 9  # most aggressive version probing
nmap -O target                  # OS detection
nmap -A target                  # aggressive: -sV -O -sC + traceroute
```

## Nmap Scripting Engine (NSE)

```bash
nmap -sC target                       # default safe scripts
nmap --script vuln target             # known-vuln checks
nmap --script "http-*" -p 80 target   # all http scripts
nmap --script smb-enum-shares,smb-enum-users -p 445 target
nmap --script ssl-enum-ciphers -p 443 target   # TLS audit
# Scripts live in /usr/share/nmap/scripts/
```

## Timing, evasion, output

```bash
# Timing templates T0 (slow/stealth) .. T5 (insane/fast)
nmap -T4 target
nmap --min-rate 1000 target

# Evasion
nmap -f target                 # fragment packets
nmap -D RND:10 target          # decoy scan (hide among fake IPs)
nmap --source-port 53 target   # spoof source port
nmap -sS -Pn -f -D RND:5 target

# Output formats
nmap -oN out.txt target        # normal
nmap -oG out.grep target       # greppable
nmap -oX out.xml target        # XML
nmap -oA basename target       # all three at once
```

## Handy one-liners

```bash
# Full recon in one command
nmap -p- -sV -sC -O -T4 -oA fullscan target

# Vuln sweep on web ports
nmap -p80,443 --script "http-enum,http-title,vuln" target

# Convert XML to HTML report
xsltproc out.xml -o report.html
```

## Tips

- Root/sudo enables `-sS`, `-O`, and raw-packet features.
- `-p-` + `--min-rate` first, then deep-scan only open ports (saves time).
- UDP is slow - scan a focused list (`53,67,123,161,500`) unless you have time.

## Resources

- [Nmap reference guide](https://nmap.org/book/man.html)
- [NSE script docs](https://nmap.org/nsedoc/)
