# Wireshark

Wireshark is the leading **network protocol analyzer**. It captures packets off a network interface and decodes them so you can inspect exactly what devices are sending - useful for troubleshooting, extracting credentials from plaintext protocols, analyzing malware traffic, and incident forensics. `tshark` is its command-line sibling.

## Capture basics

```text
1. Pick the interface (eth0 / wlan0 / Wi-Fi) on the start screen.
2. Click the blue shark-fin to start; red square to stop.
3. Save/open .pcap files for offline analysis.
```

Capture on the CLI:

```bash
tshark -i eth0 -w capture.pcap          # capture to file
tshark -r capture.pcap                  # read a file
tcpdump -i eth0 -w capture.pcap         # (tcpdump also writes pcap Wireshark reads)
```

## Capture vs display filters (important distinction)

- **Capture filters** (BPF syntax) decide what gets recorded - set before capture.
- **Display filters** decide what's shown - applied anytime after.

```text
# Capture filter (BPF)
host 10.0.0.5
port 80
tcp and not port 22

# Display filter (Wireshark syntax)
ip.addr == 10.0.0.5
http.request.method == "POST"
tcp.port == 443
dns
```

## Most-used display filters

```text
http                         # all HTTP
http.request                 # requests only
http contains "password"     # payload search
ip.src == 10.0.0.5           # by source IP
tcp.flags.syn == 1 && tcp.flags.ack == 0   # SYN packets (scan detection)
tcp.stream eq 3              # a specific TCP conversation
frame contains "flag{"       # raw byte search
ftp || telnet                # cleartext creds protocols
```

## Analysis workflows

```text
# Follow a full conversation reassembled
Right-click a packet > Follow > TCP/HTTP Stream

# Extract transferred files (images, binaries) from HTTP
File > Export Objects > HTTP

# Big-picture stats
Statistics > Protocol Hierarchy      # what protocols dominate
Statistics > Conversations           # who talks to whom, how much
Statistics > Endpoints
```

## Security use cases

- **Sniff cleartext creds** - FTP, Telnet, HTTP Basic, POP3 send passwords in plaintext.
- **Investigate scans/DoS** - spot SYN floods, port sweeps via TCP flags.
- **Malware/C2 analysis** - identify beaconing, suspicious DNS, exfil.
- **Decrypt TLS** - if you have the server key or `SSLKEYLOGFILE`, Wireshark can decrypt HTTPS.

## Tips

- You usually need **root/admin** (or membership in the `wireshark` group) to capture.
- Use a colorizing rule set and the packet-bytes pane to read payloads.
- For headless/remote, capture with `tcpdump`/`tshark` and analyze the pcap in Wireshark GUI later.

## Resources

- [Wireshark User's Guide](https://www.wireshark.org/docs/wsug_html_chunked/)
- [Wireshark display filter reference](https://www.wireshark.org/docs/dfref/)
