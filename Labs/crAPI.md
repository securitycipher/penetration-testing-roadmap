# crAPI (Completely Ridiculous API)

crAPI is a free, intentionally vulnerable application built by OWASP to teach **API security**. It models a car-service app (users, vehicles, mechanics, a shop) and is deliberately riddled with the [OWASP API Top 10](../API%20Security/OWASP%20API%20Top%2010.md) bugs. If you want to practice API testing specifically - not generic web bugs - this is the target to use.

## Run it

```bash
# Docker Compose (easiest)
curl -o docker-compose.yml https://raw.githubusercontent.com/OWASP/crAPI/main/deploy/docker/docker-compose.yml
docker compose -f docker-compose.yml --compatibility up -d
# app on http://localhost:8888, mailhog (for OTP emails) on http://localhost:8025
```

## What you practice

- **BOLA (API1)** - access other users' vehicles and orders by changing IDs
- **Broken auth (API2)** - weak [JWT](../Vulnerabilities/JWT%20Attacks.md) handling, OTP brute force
- **Property-level auth / [mass assignment](../Vulnerabilities/Mass%20Assignment.md) (API3)** - tamper with order and balance fields
- **BFLA (API5)** - reach mechanic/admin functions as a normal user
- **SSRF (API7)** - via the mechanic report feature
- **Improper inventory (API9)** - old/undocumented endpoints

## How to approach it

1. Register, then proxy all traffic through [Burp Suite](../Tools/Burp%20Suite.md).
2. Pull the API shape from the app and Postman collection it ships with.
3. Make two accounts and hammer BOLA/BFLA first (the highest-value API bugs).
4. Work the rest of the API Top 10 methodically.

## Resources

- [crAPI on GitHub](https://github.com/OWASP/crAPI)
- [OWASP API Top 10](../API%20Security/OWASP%20API%20Top%2010.md), [REST API Testing](../API%20Security/REST%20API%20Testing.md)
