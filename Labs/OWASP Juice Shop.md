# OWASP Juice Shop

OWASP Juice Shop is a free, modern, intentionally vulnerable web application. It is a single-page app (Angular + Node.js/Express) packed with real-world bugs mapped to the [OWASP Top 10](../OWASP%20Top%2010/OWASP%20Top%2010.md), with a built-in scoreboard that turns learning into a gamified challenge hunt. It is one of the best free targets for practicing web testing.

## Why it is good practice

- Realistic modern stack (JS SPA + REST API), not a dated PHP app
- 100+ challenges from trivial to hard, each tied to a vulnerability class
- A hidden **scoreboard** tracks progress and gives hints
- Safe and legal - you run it yourself

## Run it

```bash
# Docker (easiest)
docker run --rm -p 3000:3000 bkimminich/juice-shop
# then browse http://localhost:3000

# Or with Node
git clone https://github.com/juice-shop/juice-shop
cd juice-shop && npm install && npm start
```

## What to practice

- [SQL Injection](../Vulnerabilities/SQL%20Injection.md) in login and search
- [XSS](../Vulnerabilities/XSS.md) (DOM and stored)
- [Broken Access Control](../OWASP%20Top%2010/Broken%20Access%20Control.md) and [IDOR](../Vulnerabilities/IDOR.md) on the REST API
- [JWT Attacks](../Vulnerabilities/JWT%20Attacks.md) and auth flaws
- [Business Logic Vulnerabilities](../Vulnerabilities/Business%20Logic%20Vulnerabilities.md) (coupons, baskets)

## How to approach it

1. Find the scoreboard first (that is itself challenge #1).
2. Proxy the app through [Burp Suite](../Tools/Burp%20Suite.md) and watch the API traffic.
3. Work by difficulty star rating; use the official companion guide only when stuck.

## Resources

- [Juice Shop on GitHub](https://github.com/juice-shop/juice-shop)
- [Pwning OWASP Juice Shop (free book)](https://pwning.owasp-juice.shop/)
- Pairs well with the [Web Security Academy](Web%20Security%20Academy.md)
