# What is Mass Assignment?

Mass assignment (also called autobinding or object injection) happens when an app automatically maps request parameters straight onto an object or database record. If the app binds *every* field the client sends - not just the ones it intended - an attacker can set properties they should never control, like `role`, `isAdmin`, `balance`, or `verified`. It is OWASP **API3:2023** territory and extremely common in modern frameworks.

## How it works

Frameworks make binding convenient:

```python
# VULNERABLE - binds whatever the client sends
user = User(**request.json)
user.save()
```

The developer expects `{"name": "...", "email": "..."}`. The attacker sends:

```json
{ "name": "bob", "email": "bob@x.com", "role": "admin", "verified": true }
```

If `role` and `verified` are real model fields, they just got set.

## Where to look

- Registration and profile-update endpoints (set `role`, `isAdmin`, `accountType`)
- Anything that creates or edits an object via JSON/form bodies
- Fields visible in `GET` responses but "read-only" in the UI - try writing them back
- Nested objects (`"account": {"balance": 999999}`)

## Test approach

1. Capture a legitimate create/update request.
2. Look at the full object in a `GET` response to learn the real field names.
3. Add the sensitive fields to your request and resend.
4. Confirm the privileged value stuck (check your role, balance, verification, etc.).

```http
PATCH /api/v1/users/me HTTP/1.1
Content-Type: application/json

{"displayName":"bob","role":"admin","emailVerified":true,"credits":100000}
```

## Tools

- [Burp Suite](../../Tools/Burp%20Suite.md) Repeater to add fields and replay
- Diff `GET` vs editable UI to discover hidden writable fields
- Practice on [crAPI](../../Labs/crAPI.md)

## Mitigation

- Use an **allowlist** of fields the client may set (DTOs, `fillable`/`permit`, explicit field maps).
- Never bind request bodies directly to ORM models.
- Keep sensitive fields (`role`, `isAdmin`, `balance`) server-controlled only.
- Separate read and write models so read-only fields cannot be written.

## CWE

- CWE-915: Improperly Controlled Modification of Dynamically-Determined Object Attributes

## Deep dive

- [OWASP API Top 10 - API3 (property level authorization)](../API%20Security/OWASP%20API%20Top%2010.md)
- [OWASP mass assignment cheat sheet](https://cheatsheetseries.owasp.org/cheatsheets/Mass_Assignment_Cheat_Sheet.html)
