# What is Single Sign-On (SSO)?

Single Sign-On (SSO) is a system that allows you to use one set of login credentials (like username and password) to access multiple applications or services. In simpler terms, it's like having one key that unlocks multiple doors.

## How Does SSO Work?

Imagine you have several accounts for different websites or applications—maybe one for email, one for social media, and one for your work. With traditional login methods, you'd need a separate set of credentials (username and password) for each.

Now, enter SSO. When you use SSO, you log in once, and that login information is used to grant you access to multiple services without requiring you to log in again for each one.

## Key Components of SSO
- Identity Provider (IDP): This is like the master key holder. The Identity Provider is where you initially log in. It could be a service like Google, Microsoft, or an organization's internal authentication system.
- Service Provider (SP): These are the individual applications or services you want to access. They rely on the Identity Provider to confirm your identity.
- User: That's you! The person using the system.

## Example Scenario:

Let's say you use Google as your Identity Provider (IDP) and you want to access your email (Gmail) and a project management tool (like Asana) with SSO.

- You visit Gmail and click the "Login with Google" button.
- Instead of entering a separate Gmail username and password, Google's Identity Provider checks if you're already logged in. If yes, you're granted access to Gmail without needing to log in again.
- Now, you decide to work on a project using Asana. You click "Login with Google" there too.
- Asana's Service Provider talks to Google's Identity Provider, which confirms your identity. Since you're already logged in, you're seamlessly logged into Asana without having to enter another set of credentials.

## Benefits of SSO:
- Convenience: You only need to remember one set of credentials, making life easier and reducing the risk of forgotten passwords.
- Security: Since you're not managing multiple passwords, there's less chance of using weak or easily guessable passwords. Also, the centralized Identity Provider can enforce strong security measures.
- Time-Saving: Logging in once and gaining access to multiple services saves time and reduces the frustration of repeated logins.

In essence, Single Sign-On is like having a universal key that opens the doors to all your online destinations, making your digital life simpler and more secure.

---

## SSO from a pentester's view

SSO is a **high-value target**: compromise the Identity Provider (IdP) or its trust with a Service Provider (SP) and you unlock *every* connected app — one key, all the doors.

### Attack surface

- **The IdP itself** — phishing/password spraying the central login (no MFA = mass ATO). See [Azure](../Cloud/Azure.md) for Entra ID spraying.
- **The federation protocol** — [SAML](SAML.md) (signature wrapping, Golden SAML) and [OAuth 2.0](OAuth%202.0.md)/OIDC (`redirect_uri`, `state`, token confusion).
- **Session handling** — a stolen IdP session cookie can pivot to all SPs (see [Session Hijacking](../Vulnerabilities/Session%20Hijacking.md)).
- **Account linking** — force-link an attacker identity to a victim account (CSRF on the link flow).
- **SP trust misconfig** — an SP that accepts assertions/tokens without validating issuer, audience, or signature.

### How to test

```bash
# 1. Map the SSO flow in Burp (which protocol? SAML vs OIDC?).
# 2. SAML -> use SAML Raider (XSW, strip signature, edit NameID).
# 3. OIDC/OAuth -> tamper redirect_uri, remove state, swap tokens.
# 4. Test the IdP login for MFA enforcement + password spraying.
```

### Why it matters

Because SSO centralizes trust, a single flaw is often **critical** — it can equal full-organization compromise. Always test MFA enforcement at the IdP.

## Related

- [SAML](SAML.md) · [OAuth 2.0](OAuth%202.0.md) · [MFA vs 2FA](MFA%20vs%202FA.md)
- [Identification and Authentication Failures](../OWASP%20Top%2010/Identification%20and%20Authentication%20Failures.md)
