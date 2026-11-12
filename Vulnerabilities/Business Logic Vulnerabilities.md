# What are Business Logic Vulnerabilities?

Business logic flaws are bugs in how an application is *supposed* to work, not in a specific unsafe function. The code runs exactly as written - the problem is that the workflow allows something the business never intended. Scanners miss these almost entirely, which is why they are some of the most valuable findings in bug bounty and pentests.

## Why they are different

There is no payload that always triggers them. You find them by understanding the app's intended flow and then asking "what if I do this out of order, change this value, or skip this step?" They require thinking like a user who is trying to cheat.

## Common classes with examples

- **Negative or extreme values**
  - Buy quantity `-1` to get a refund/credit
  - Apply a discount that exceeds the price for a negative total
- **Price / parameter tampering**
  - Change `price=100` to `price=1` in the request
  - Swap `currency` to a cheaper one after the amount is set
- **Workflow / step skipping**
  - Jump straight to the "payment confirmed" step without paying
  - Reach `/checkout/success` directly
- **Coupon and limit abuse**
  - Reuse a one-time coupon by racing requests (see [Race Condition](Race%20Condition.md))
  - Stack discounts that should be mutually exclusive
- **Trusting client-side controls**
  - Re-enable a disabled button or bypass JS validation
  - Change a hidden `isAdmin=false` field
- **Insufficient quantity/authorization checks**
  - Transfer more money than the balance
  - Approve your own request in a maker-checker flow

## How to test

1. Map the intended workflow completely (every step, every value).
2. For each step ask: what if I skip it, repeat it, reorder it, or tamper with a value?
3. Try negative numbers, zero, huge numbers, and type changes.
4. Use two accounts to test who-can-do-what.
5. Replay the final "success" request directly with [Burp Suite](../../Tools/Burp%20Suite.md).

## Tools

- [Burp Suite](../../Tools/Burp%20Suite.md) Repeater (reorder/replay) and Turbo Intruder (races)
- Your brain - this is the least automatable vuln class

## Mitigation

- Enforce all business rules **server-side**, never trust the client.
- Validate state transitions (you cannot confirm an order that was not paid).
- Re-check authorization and limits at every step, not just the first.
- Add server-side checks for quantities, prices, and totals independent of input.

## CWE

- CWE-840: Business Logic Errors
- CWE-841: Improper Enforcement of Behavioral Workflow

## Deep dive

- [PortSwigger business logic labs](https://portswigger.net/web-security/logic-flaws)
- [Insecure Design - OWASP Top 10](../OWASP%20Top%2010/Insecure%20Design.md)
