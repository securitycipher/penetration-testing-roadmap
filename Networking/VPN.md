# What is a VPN?
VPN stands for Virtual Private Network. It's a service that allows you to create a secure connection to another network over the internet. In simpler terms, it's like creating a private tunnel between your device (like your computer or smartphone) and the internet, which keeps your online activities secure and private.

## How does it work?
When you connect to the internet normally, your device sends data through your Internet Service Provider (ISP) to access websites and services. This data can potentially be intercepted or monitored by your ISP, hackers, or even government agencies.

However, when you use a VPN, your data is encrypted before it leaves your device and travels through the VPN server. This encryption makes it extremely difficult for anyone to intercept or decipher your data. So, even if someone manages to intercept your data, all they'll see is a jumbled mess of characters.

## Why use a VPN?
- Privacy: A VPN hides your IP address and encrypts your internet traffic, making it much harder for anyone to track your online activities.

- Security: It adds an extra layer of security, especially when you're using public Wi-Fi networks like those in cafes, airports, or hotels, which are often less secure and more vulnerable to hackers.

- Access geo-blocked content: Some websites or streaming services may be restricted to certain geographic regions. By connecting to a VPN server in a different country, you can bypass these restrictions and access content as if you were physically located there.

- Bypass censorship: In some countries, certain websites or services may be blocked by the government. A VPN can help you bypass these censorship efforts and access the open internet.

## How to use a VPN?
Using a VPN is usually quite simple. You'll typically need to:

- Choose a VPN provider: There are many VPN services available, both free and paid. It's essential to choose a reputable one that prioritizes privacy and security.

- Download and install the VPN app: Most VPN providers offer apps for various devices and operating systems. Simply download the app from the provider's website or app store and follow the installation instructions.

- Connect to a VPN server: Once you've installed the app, launch it and choose a server location to connect to. The VPN app will handle the rest, encrypting your connection and rerouting your internet traffic through the selected server.

- Browse the internet securely: That's it! You're now connected to the internet via the VPN, and your online activities are encrypted and secure. You can browse the web, stream content, or use online services with peace of mind.

In summary, a VPN is a powerful tool for enhancing your online privacy, security, and freedom. By encrypting your internet connection and masking your IP address, it helps keep your online activities private and secure from prying eyes. Whether you're concerned about privacy, security, accessing geo-blocked content, or bypassing censorship, a VPN can be an invaluable tool for internet users of all levels of expertise.

---

## VPNs from a pentester's view

Corporate VPNs (SSL-VPN gateways, IPsec) are internet-facing entry points and a recurring source of critical CVEs. They also provide the network foothold that turns an external test into an internal one.

### What to test

- **Known CVEs on the gateway** — Fortinet, Pulse Secure/Ivanti, Citrix, SonicWall have all had pre-auth RCE / auth-bypass bugs.
- **Weak / no MFA** — password spray captured or guessed corporate creds.
- **Username enumeration** on the login portal.
- **IKE/IPsec aggressive mode** — grab a PSK hash for offline cracking.
- **Split tunneling / overly broad access** once connected.

### Commands

```bash
# Fingerprint the VPN gateway
nmap -sV -p 443,500,4500 vpn.target.com
nmap -sU -p 500 --script ike-version vpn.target.com

# IKE aggressive mode PSK capture (IPsec)
ike-scan -A -M -P vpn.target.com          # -P saves the PSK hash
psk-crack -d wordlist.txt psk.txt

# Password spray the SSL-VPN portal (respect lockout!)
# (use a purpose-built module or curl the login endpoint)

# Check the gateway version against known exploits
searchsploit fortinet ssl vpn
```

### Why it matters

A compromised VPN account or an unpatched gateway drops you straight into the internal network — often the single highest-impact finding on an external engagement, chaining directly into [Active Directory](../Active%20Directory/Active%20Directory%20Basics.md) attacks.

### Mitigation

- Patch gateways promptly; subscribe to vendor advisories.
- Enforce **MFA** on all VPN logins.
- Restrict access with device certificates / posture checks.
- Disable IKE aggressive mode; use strong PSKs or certificate auth.

## Related

- [Common Protocols](Common%20Protocols.md)
- [Active Directory Basics](../Active%20Directory/Active%20Directory%20Basics.md)
