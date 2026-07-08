# What is a Race Condition?

A Race Condition is a flaw where the outcome depends on the timing of events that are supposed to happen in order. In web apps this usually shows up as a time-of-check to time-of-use (TOCTOU) gap: the app checks a condition, then acts on it, and an attacker slips a second request into that window. Think redeeming one gift card twice, withdrawing more than your balance, or bypassing a rate limit.

## How it works (TOCTOU)

The app does a check, then an action, with a gap between:

```text
1. CHECK:  balance >= 100 ?           (read)
2. ...tiny delay...
3. USE:    balance = balance - 100    (write)
```

If you fire many requests so they all reach step 1 **before** any reaches step 3, they all see the original balance and all succeed - you withdraw 100 many times from a balance of 100.

## Common vulnerable actions

- Redeem a gift card / coupon multiple times
- Withdraw / transfer more than your balance
- Bypass a rate limit or one-time action (single vote, single signup bonus)
- Apply a discount repeatedly; over-purchase limited stock
- Multi-step flows where a limit is checked in one step and used in another

## The technique - overlap the requests

The key is minimising the timing window. Best method: Burp's **single-packet attack** (Turbo Intruder or Repeater tab groups) which sends ~20-30 HTTP/2 requests in one TCP packet so they arrive simultaneously.

**Burp Repeater (easiest):**

```text
1. Send the target request to Repeater
2. Duplicate the tab ~20 times (or add to a tab group)
3. Group -> "Send group in parallel (single-packet attack)"
4. Compare responses: did more than one succeed?
```

**Turbo Intruder script:**

```python
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint, concurrentConnections=1,
                           engine=Engine.BURP2)   # HTTP/2 single packet
    for i in range(30):
        engine.queue(target.req, gate='race1')
    engine.openGate('race1')     # release all at once

def handleResponse(req, interesting):
    table.add(req)
```

**Quick-and-dirty with curl (less precise):**

```bash
for i in $(seq 1 30); do
  curl -s -X POST https://target.tld/coupon/redeem \
    -H "Cookie: session=..." -d "code=SAVE50" &
done; wait
```

## Full walkthrough

1. Balance is 100. `POST /withdraw amount=100` normally allowed once.
2. Duplicate the request 20x in Burp, send as a single-packet group.
3. 5 of them return "success" before the balance updates.
4. Final balance is -400 -> you extracted 5x the money. Race condition confirmed.

## Tools

- [Turbo Intruder](https://github.com/PortSwigger/turbo-intruder) - precise parallel requests
- Burp Repeater **"Send group in parallel"** - built-in single-packet attack
- [race-the-web](https://github.com/TheHackerDev/race-the-web) - CLI race tester

## Mitigation - the fix

- Enforce limits **atomically in the database**: unique constraints, `SELECT ... FOR UPDATE`, atomic `UPDATE ... WHERE balance >= 100`.
- Use locks (pessimistic/optimistic), transactions, or **idempotency keys**.
- Never rely on read-then-write logic in application code alone.
- Rate-limit and dedupe one-time operations server-side.

```sql
-- SAFE: single atomic statement, no check-then-act gap
UPDATE accounts SET balance = balance - 100
WHERE id = :id AND balance >= 100;   -- affects 0 rows if insufficient
```

## Practice

- [PortSwigger race condition labs](https://portswigger.net/web-security/race-conditions)

## Deep dive

- [Race Condition - Vulnerability Explain](https://securitycipher.com/vulnerability-explain/)
- [PortSwigger race condition labs](https://portswigger.net/web-security/race-conditions)

## CWE

- CWE-362: Concurrent Execution using Shared Resource with Improper Synchronization
- CWE-367: Time-of-check Time-of-use (TOCTOU) Race Condition
