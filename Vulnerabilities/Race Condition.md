# What is a Race Condition?

A Race Condition is a flaw where the outcome depends on the timing of events that are supposed to happen in order. In web apps this usually shows up as a time-of-check to time-of-use (TOCTOU) gap: the app checks a condition, then acts on it, and an attacker slips a second request into that window. Think redeeming one gift card twice, withdrawing more than your balance, or bypassing a rate limit.

## How it works

- The app validates state (balance, coupon used, request count)
- There is a small delay before it updates that state
- Firing many requests in parallel lets several pass the same check

## Test approach

```text
# Single-packet attack: send many requests so they arrive at once
# Example targets:
POST /coupon/redeem   code=SAVE50   x40 in parallel
POST /withdraw        amount=100    x20 in parallel (balance=100)
POST /vote            id=42         x50 in parallel
```

```python
# Rough parallel-fire idea with Python threads (Burp Turbo Intruder is better)
import threading, requests
def fire():
    requests.post("https://target.tld/coupon/redeem",
                  cookies={"session": "..."}, data={"code": "SAVE50"})
for _ in range(40):
    threading.Thread(target=fire).start()
```

## Tools

- [Turbo Intruder](https://github.com/PortSwigger/turbo-intruder) - Burp extension for precise parallel requests
- [Burp Repeater "Send group in parallel"](https://portswigger.net/burp) - single-packet attack
- [race-the-web](https://github.com/TheHackerDev/race-the-web) - CLI race tester

## Manual testing

1. Find an action that should only happen once or has a limit
2. Capture the request and send 20-50 copies as close to simultaneously as possible
3. Use Turbo Intruder or the single-packet attack to shrink the timing window
4. Check whether the limit was exceeded (double redemption, negative balance)

## Mitigation

- Enforce limits atomically in the database (unique constraints, `SELECT ... FOR UPDATE`)
- Use locks, transactions, or idempotency keys for sensitive actions
- Do not rely on read-then-write logic in application code alone
- Rate-limit and add server-side dedupe for one-time operations

## Deep dive

- [Race Condition - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger race condition labs](https://portswigger.net/web-security/race-conditions)

## CWE

- CWE-362: Concurrent Execution using Shared Resource with Improper Synchronization
- CWE-367: Time-of-check Time-of-use (TOCTOU) Race Condition
