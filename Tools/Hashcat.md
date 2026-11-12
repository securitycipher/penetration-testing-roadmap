# Hashcat

Hashcat is the fastest password-cracking tool, built to use your GPU. In a pentest you use it offline on hashes you have already dumped - from a database, a SAM/NTDS dump, a captured Wi-Fi handshake, or a [JWT](../Vulnerabilities/JWT%20Attacks.md). It does not attack live logins (that is brute-forcing, use Hydra for that).

## Install

```bash
sudo apt install hashcat        # Debian/Kali
brew install hashcat            # macOS
hashcat -I                      # list available compute devices
```

## The basic command

```bash
hashcat -m <mode> -a <attack> hashes.txt wordlist.txt
```

- `-m` = hash type (mode number)
- `-a` = attack mode (0 = wordlist, 3 = mask/brute, 6/7 = combinator)

## Common hash modes

```text
0      MD5
100    SHA1
1000   NTLM (Windows)
1800   sha512crypt ($6$, Linux /etc/shadow)
3200   bcrypt
5600   NetNTLMv2 (Responder captures)
13100  Kerberoasting (TGS-REP, etc23)
18200  AS-REP roasting
16500  JWT (HS256)
22000  WPA-PBKDF2 (Wi-Fi handshakes/PMKID)
```

Not sure which? `hashcat --example-hashes | grep -i <type>` or use `hashid`.

## Attack examples

```bash
# Straight wordlist
hashcat -m 1000 ntlm.txt rockyou.txt

# Wordlist + rules (hugely more effective)
hashcat -m 1000 ntlm.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule

# Mask / brute force: 8 chars, upper+lower+digit
hashcat -m 1000 ntlm.txt -a 3 ?u?l?l?l?l?l?d?d

# Crack a Kerberoasting hash
hashcat -m 13100 kerb.txt rockyou.txt -r rules/best64.rule

# Crack a Wi-Fi capture (see OSWP workflow)
hashcat -m 22000 capture.hc22000 rockyou.txt
```

## Useful flags

```bash
--show                 # print already-cracked hashes from the potfile
--username             # input file has user:hash format
-O                     # optimized kernels (faster, length limits)
-w 3                   # workload profile (3 = high)
--status               # live status output
```

## Tips

- Rules (`best64`, `dive`, custom) beat bigger wordlists most of the time.
- Cracked results live in `~/.local/share/hashcat/hashcat.potfile`.
- Feed it hashes from [Impacket](Impacket.md) secretsdump, [DCSync](../Active%20Directory/DCSync.md), or Responder.

## Resources

- [Hashcat wiki](https://hashcat.net/wiki/)
- [Hashcat example hashes](https://hashcat.net/wiki/doku.php?id=example_hashes)
