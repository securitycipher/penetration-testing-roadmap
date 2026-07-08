# MFA vs 2FA
Let's break down MFA (Multi-Factor Authentication) and 2FA (Two-Factor Authentication) in a way that's easy to understand.

## Two-Factor Authentication (2FA)

Imagine you have a treasure chest, and you want to keep it secure. You decide to use a lock as your first layer of defense. This lock requires a key to open, and only you have that key. This is like your username and password combination in the digital world. It's a single factor - something you know.

But what if someone else gets hold of your key (password)? That's where Two-Factor Authentication (2FA) comes into play. In addition to the key (password), you add a second layer of protection. This second layer could be something you have, like a special code sent to your phone or email. So now, even if someone has your password, they still need that second piece to access your treasure chest.

In simple terms, 2FA is like having two locks on your treasure chest - one that requires a key (password) and another that requires a unique code (something you have).

## Multi-Factor Authentication (MFA)

Now, let's take the security of your treasure chest to the next level with Multi-Factor Authentication (MFA). Instead of just two layers, MFA involves adding multiple layers of protection.

In addition to the lock and key (password) and the unique code (something you have), you might introduce a third layer. This could be something you are, like a fingerprint or a face scan. So, even if someone somehow manages to get your password and the unique code, they still need your fingerprint or face to unlock the chest.

In summary, MFA is like fortifying your treasure chest with more than just two locks - it adds an extra layer, making it even more challenging for unauthorized individuals to gain access.

To relate it back to the digital world, using MFA means combining different types of authentication methods (password, unique codes, biometrics) to enhance the security of your online accounts. It's like having a digital fortress with multiple barriers to keep your information safe.

---

## The three authentication factors

- **Something you know** — password, PIN.
- **Something you have** — phone, hardware token (YubiKey), authenticator app.
- **Something you are** — fingerprint, face scan.

> **2FA is a subset of MFA.** 2FA = exactly two factors; MFA = two *or more*. All 2FA is MFA, but MFA can use three+.

## Bypassing MFA (pentester's view)

MFA raises the bar but is not unbreakable — test how it's implemented.

- **MFA fatigue / push bombing** — spam approval prompts until the user taps "Approve."
- **SIM swapping / SMS interception** — SMS OTP is the weakest factor (SS7, SIM swap).
- **Real-time phishing (AiTM)** — proxy the login (Evilginx) to capture the session cookie *after* MFA:

```bash
# Evilginx acts as a man-in-the-middle proxy capturing post-MFA session tokens
evilginx2      # set up phishlet for the target IdP
```

- **OTP flaws** — no rate limit (brute the 6-digit code), OTP reuse, race conditions.
- **Backup/recovery bypass** — weak "lost device" flows skip MFA.
- **Session/token theft** — steal a valid post-MFA cookie/token (renders MFA moot).
- **Legacy protocol bypass** — endpoints (e.g., IMAP/legacy auth) that don't enforce MFA.

## What to check

```
- Is MFA enforced everywhere, or only on some endpoints?
- Is the OTP rate-limited and single-use?
- Is SMS the only option (weak)? Are phishing-resistant factors (FIDO2/WebAuthn) available?
- Does stealing the session cookie bypass MFA entirely?
```

Phishing-resistant **FIDO2/WebAuthn** hardware keys defeat most of these bypasses.

## Related

- [Identification and Authentication Failures](../OWASP%20Top%2010/Identification%20and%20Authentication%20Failures.md)
- [SSO](SSO.md) · [Session Hijacking](../Vulnerabilities/Session%20Hijacking.md)
