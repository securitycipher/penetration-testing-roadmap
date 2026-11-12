# Gobuster

Gobuster is a fast, simple brute-forcing tool for discovering hidden content: directories and files, DNS subdomains, and virtual hosts. It is a go-to during web recon when you want to find pages and endpoints that are not linked anywhere. It overlaps with [ffuf](ffuf.md) - Gobuster is simpler, ffuf is more flexible.

## Install

```bash
sudo apt install gobuster     # Debian/Kali
go install github.com/OJ/gobuster/v3@latest
```

## Modes

- `dir` - directories and files on a web server
- `dns` - subdomains of a domain
- `vhost` - virtual hosts on the same IP
- `fuzz` - generic FUZZ-keyword fuzzing

## Directory and file discovery

```bash
gobuster dir -u http://target.tld -w /usr/share/wordlists/dirb/common.txt

# Look for specific extensions
gobuster dir -u http://target.tld -w wordlist.txt -x php,txt,bak,config

# Add threads, status filtering, and auth
gobuster dir -u http://target.tld -w big.txt -t 50 -b 404,403 -c "session=abc"
```

## Subdomain enumeration

```bash
gobuster dns -d target.tld -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

See also [Subdomain Enumeration](../Recon/Subdomain%20Enumeration.md).

## Virtual host discovery

```bash
gobuster vhost -u http://target.tld -w vhosts.txt --append-domain
```

## Useful flags

```bash
-t 50          # threads (speed)
-x php,html    # extensions
-s 200,301     # only show these status codes
-b 404         # blacklist these status codes
-k             # skip TLS verification
-o out.txt     # save output
```

## Tips

- Wordlist quality matters more than speed - use SecLists.
- Start small (`common.txt`), then go bigger (`raft-*`, `directory-list-2.3-medium`).
- For recursion, parameter fuzzing, and filtering by size/words, reach for [ffuf](ffuf.md).

## Resources

- [Gobuster on GitHub](https://github.com/OJ/gobuster)
- [SecLists wordlists](https://github.com/danielmiessler/SecLists)
