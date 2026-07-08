# Common Protocols
In the context of computer networking, a protocol is a set of rules and conventions that govern how data is transmitted and received between devices on a network. These rules ensure that different devices can communicate effectively with each other. Here, we'll explore some common protocols to help you understand their roles in networking.

## Transmission Control Protocol (TCP)

- Purpose: TCP is a connection-oriented protocol that ensures reliable and ordered delivery of data between devices.
- Characteristics: It establishes a connection before data transfer, breaks data into packets, numbers and sequences them, and ensures they are received correctly.
## Internet Protocol (IP)

- Purpose: IP is responsible for addressing and routing packets of data so they can travel across networks and arrive at the correct destination.
- Characteristics: There are two main versions, IPv4 and IPv6. IPv4 uses 32-bit addresses, while IPv6 uses 128-bit addresses to accommodate the growing number of devices on the internet.
## Hypertext Transfer Protocol (HTTP)

- Purpose: HTTP is the foundation of any data exchange on the web. It governs the communication between web browsers and servers.
- Characteristics: It operates over TCP and is used for transmitting hypertext (text with links) and multimedia content.
## Hypertext Transfer Protocol Secure (HTTPS)

- Purpose: Similar to HTTP, but with an added layer of security through encryption (SSL/TLS). It ensures that data exchanged between the user and the website remains confidential.
## File Transfer Protocol (FTP)

- Purpose: FTP is used for transferring files between a client and a server on a network.
- Characteristics: It allows for uploading and downloading files, and can operate in either active or passive mode.
## Simple Mail Transfer Protocol (SMTP)

- Purpose: SMTP is used for sending emails.
- Characteristics: It works with other protocols like POP3 or IMAP to deliver emails to the recipient's mailbox.
## Post Office Protocol version 3 (POP3) and Internet Message Access Protocol (IMAP)

- Purpose: These protocols are used by email clients to retrieve messages from a mail server.
- Characteristics: POP3 downloads emails to the local device, while IMAP allows users to view and manipulate emails on the server without downloading them.

Understanding these common protocols is a foundational step in grasping how devices communicate over networks. As you delve deeper into networking, you'll encounter many more protocols, each serving specific purposes in the vast world of information exchange.

---

## Protocols from a pentester's view

Every open port maps to a protocol, and each protocol has known ports, enumeration tricks, and attacks. Memorize this table — it drives what you do after a port scan.

| Port | Protocol | What to test |
|------|----------|--------------|
| 21 | FTP | Anonymous login, cleartext creds, `ftp` brute force |
| 22 | SSH | Weak creds, key auth, version CVEs |
| 23 | Telnet | Cleartext creds — sniff or brute force |
| 25/465/587 | SMTP | User enum (`VRFY`), open relay |
| 53 | DNS | Zone transfer (`AXFR`), subdomain enum |
| 80/443 | HTTP(S) | Full web app pentest |
| 88 | Kerberos | AS-REP roasting, Kerberoasting |
| 110/143 | POP3/IMAP | Cleartext creds, brute force |
| 139/445 | SMB | Null sessions, shares, EternalBlue, relay |
| 161 | SNMP | `public` community string → device info dump |
| 389/636 | LDAP | Anonymous bind, user enum |
| 3306/5432/1433 | MySQL/Postgres/MSSQL | Weak creds, `xp_cmdshell` |
| 3389 | RDP | Brute force, BlueKeep, cred spray |

### Enumeration commands

```bash
# Scan and fingerprint services
nmap -sC -sV -p- target -oN scan.txt

# SMB — shares, users, null session
smbclient -L //target -N
enum4linux-ng -A target
crackmapexec smb target -u '' -p ''

# SNMP walk with default community string
snmpwalk -v2c -c public target
onesixtyone -c community.txt target

# DNS zone transfer
dig axfr @ns.target.com target.com

# SMTP user enumeration
smtp-user-enum -M VRFY -U users.txt -t target
```

### Key takeaway

**Cleartext protocols** (FTP, Telnet, HTTP, POP3, IMAP, SNMPv1/2) leak credentials to anyone sniffing the wire — always flag them and recommend the encrypted equivalent (SFTP, SSH, HTTPS, POP3S/IMAPS, SNMPv3).

## Related

- [Nmap](../Tools/Nmap.md) · [Wireshark](../Tools/Wireshark.md)
- [OSI Model](OSI%20Model.md)
