# What is OAuth 2.0?
OAuth 2.0 (Open Authorization 2.0) is an authorization framework that allows third-party applications to obtain limited access to a user's resources on a server without exposing the user's credentials. In simpler terms, OAuth 2.0 is a protocol that enables secure access to your data on one website (or application) by another website or application, without sharing your username and password directly.

## Here are the key components and concepts of OAuth 2.0:

- Roles:
  - Resource Owner: The user who owns the data and can grant access to it. For example, you, as a user, are the resource owner.
  - Client: The application that is seeking access to the user's data. This could be a mobile app, a web application, or any other type of application.
  - Authorization Server: The server responsible for authenticating the user and obtaining their consent to grant access to the client. It issues access tokens.
  - Resource Server: The server hosting the protected resources. It verifies the access tokens and serves the requested data.
- Authorization Grant Types:
  - OAuth 2.0 defines several ways in which a client can obtain authorization. Common grant types include:
    - Authorization Code: Used by web applications. The client is redirected to the authorization server, and after authorization, it receives an authorization code that can be exchanged for an access token.
    - Implicit: Designed for client-side applications (like JavaScript running in a browser). The access token is returned directly in the URL fragment.
    - Resource Owner Password Credentials: The client collects the user's username and password directly, but it is generally discouraged due to security concerns.
    - Client Credentials: Used for server-to-server communication where the client is the owner of the resource.
- Access Tokens:
  - An access token is a credential that represents the authorization granted to the client. It is a string that the client includes in its requests to access protected resources on behalf of the resource owner.
  - Access tokens have a limited lifespan and are used to authenticate and authorize requests during that period.
- Refresh Tokens:
  - In certain scenarios, a refresh token is issued along with the access token. The refresh token can be used to obtain a new access token without requiring the user to re-authenticate.
- Scope:
  - Scopes define the extent of access that the client is requesting. When the user grants authorization, they are specifying the scope of access the client is permitted.
- Endpoints:
  - OAuth 2.0 involves several endpoints, including the authorization endpoint (where the user grants or denies access), the token endpoint (where the client exchanges the authorization code for an access token), and others.
- Example Workflow:
  - The client requests authorization by redirecting the user to the authorization server.
  - The user authenticates and grants permission.
  - The authorization server redirects the user back to the client with an authorization code.
  - The client exchanges the authorization code for an access token.
  - The client uses the access token to access the protected resources on the resource server.

OAuth 2.0 is widely adopted and provides a flexible framework for secure and delegated access to resources. It is used by many major platforms and services to enable third-party applications to interact with user data in a secure and controlled manner.

---

## OAuth 2.0 attacks (pentester's view)

OAuth is a frequent source of account-takeover bugs — almost always due to implementation mistakes, not the protocol itself.

### Top attack vectors

- **`redirect_uri` manipulation** — if validation is loose, redirect the auth code/token to an attacker domain:

```
# Weak validation lets you steal the code
https://idp.com/authorize?client_id=X&redirect_uri=https://evil.com&response_type=code
# Also try: open redirect chains, path append, subdomain, ?/# tricks
redirect_uri=https://target.com.evil.com
redirect_uri=https://target.com/callback/../redirect?url=evil.com
```

- **CSRF via missing `state`** — if `state` is absent/unchecked, force-link the victim's account to the attacker's (login CSRF).
- **Stealing auth code** — via `redirect_uri` leak, Referer header, or open redirect.
- **Implicit flow token leakage** — access token in the URL fragment ends up in history/logs/Referer.
- **Overly broad scopes** — request more than the app needs; check what a token actually grants.
- **Missing PKCE** on public clients → authorization code interception.

### How to test

```bash
# 1. Capture the full OAuth flow in Burp.
# 2. Tamper with redirect_uri, remove/replay state, downgrade to implicit.
# 3. Check token audience/scope and whether the app validates them.
# 4. Try the "account link" CSRF if state is missing.
```

### Note: OAuth is authorization, not authentication

Using OAuth access tokens as proof of identity (instead of OpenID Connect ID tokens) leads to auth bypass. See [SAML](SAML.md) and [SSO](SSO.md) for federated login.

## Related

- [Identification and Authentication Failures](../OWASP%20Top%2010/Identification%20and%20Authentication%20Failures.md)
- [SSO](SSO.md) · [SAML](SAML.md)
