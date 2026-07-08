# What is an Intrusion Detection System (IDS)?
An Intrusion Detection System (IDS) is like a digital security guard for your computer network. Its primary job is to watch over the data flowing through the network and identify any unusual or suspicious activities. Just as a security guard in a physical building would look out for signs of unauthorized entry, an IDS keeps an eye on your digital network for signs of potential cyber threats.

## Why do we need IDS?
The internet is a vast and interconnected space where data travels between different devices and servers. Unfortunately, not everyone online has good intentions. Some individuals or programs may try to break into computer systems to steal information, cause damage, or disrupt normal operations. An IDS helps to detect and alert us about these malicious activities.

## How does IDS work?
Think of IDS as a digital detective with a keen sense of observation. It uses two main approaches to identify potential threats:
- Signature-Based Detection:
  - This method is similar to recognizing a known criminal's face. The IDS has a database of known attack patterns or "signatures." When it sees network traffic that matches these signatures, it raises an alarm.
  - For example, if the IDS knows what a common virus or hacking attempt looks like, it can spot those patterns and take action.
- Anomaly-Based Detection:
  - Instead of looking for known "criminals," anomaly-based detection looks for unusual behavior. It learns what normal network activity looks like and flags anything that deviates significantly from the norm.
  - For instance, if your computer typically sends a small amount of data to a specific server, and suddenly there's a massive amount of data being sent elsewhere, the IDS might detect this anomaly.

## What happens when IDS detects something?
When the IDS senses a potential threat, it doesn't intervene directly like a superhero. Instead, it alerts the network administrator or a security team. This notification allows them to investigate the situation, confirm if it's a real threat, and take appropriate action.


An IDS is your digital security guard that tirelessly watches over your network, looking for any signs of trouble. It uses both known patterns and behavioral analysis to identify potential threats, helping you stay one step ahead of cybercriminals and ensuring the safety of your digital environment.

---

## IDS from a pentester's view

An IDS **detects but does not block** (that's an [IPS](IPS.md)). During a pentest you both try to evade it and, in a purple-team context, verify it actually fires on your attacks.

### Common IDS/IPS products

- **Snort** and **Suricata** (open source, signature + rules)
- **Zeek** (formerly Bro — network analysis, anomaly focus)
- Commercial: Cisco Firepower, Palo Alto, Darktrace

### Evasion techniques

```bash
# Nmap evasion flags
nmap -sS -T2 target                 # slow scan to stay under thresholds
nmap -f target                      # fragment packets
nmap -D RND:10 target               # decoy scan (hide among fake source IPs)
nmap --data-length 25 target        # pad packets to alter signatures
nmap -g 53 target                   # spoof source port (e.g., DNS)

# Payload/protocol evasion
# - Encode/obfuscate payloads (base64, case variation, URL encoding)
# - Use HTTPS/TLS so DPI can't read payloads
# - Slow down (low-and-slow) to avoid rate-based anomaly detection
```

### How signatures are matched (why evasion works)

Signature IDS looks for exact byte patterns. Fragmentation, encoding, and encryption break the pattern so the rule never matches. Anomaly IDS instead learns a baseline — beat it by going slow and blending with normal traffic.

### Purple-team validation

```bash
# Trigger a known Snort rule to confirm the sensor is alerting
curl "http://target/?id=/etc/passwd"        # path traversal signature
# Then confirm the alert appears in the SIEM/console.
```

## Related

- [IPS](IPS.md) · [Nmap](../Tools/Nmap.md) · [Wireshark](../Tools/Wireshark.md)
