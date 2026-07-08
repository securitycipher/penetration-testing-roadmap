# Insecure Design (A04:2021)

New in 2021, Insecure Design is about flaws in the **logic and architecture** of an application - not implementation bugs. Even perfectly written code can be insecure if the *design* never included a needed control. You cannot "patch" your way out of a missing control; it has to be designed in.

**Key distinction:** a missing input-length check is an implementation bug; a password-reset flow that never limits attempts is an insecure *design*.

## Classic examples

- **No anti-automation** on a coupon/OTP endpoint -> brute force or mass abuse
- **Business logic gaps** - buy items with a negative quantity to get a refund; apply the same coupon infinitely
- **Trusting the client** - price/discount/role sent from the browser and trusted
- **No workflow enforcement** - skip the "payment" step and go straight to "order complete"
- **Recovery questions** ("mother's maiden name") - guessable by design
- **Unlimited money transfer with no limits/velocity checks**

## How to test (business logic)

1. Map the intended flow, then try to break the *sequence* or *assumptions*.
2. Send negative, zero, huge, or out-of-range values (`quantity=-1`, `amount=0`).
3. Skip steps (go directly to the final endpoint of a multi-step flow).
4. Replay one-time actions; apply discounts repeatedly.
5. Tamper values the server should compute (price, total, role).

## Example - negative quantity refund

```http
POST /cart/add HTTP/1.1
{"item":"tv","quantity":-5}      # total becomes negative -> store "owes" you money at checkout
```

## Example - skipping payment

```text
Intended: /cart -> /checkout -> /pay -> /confirm
Attack:   POST /confirm directly (order marked paid without /pay)
```

## Mitigation - the fix

- **Threat model** during design (STRIDE) - see [Threat modeling](../Threat%20modeling/).
- Establish **secure design patterns** and a paved-road reference architecture.
- Enforce **business rules server-side**; recompute money/roles on the server.
- Add **anti-automation** (rate limits, CAPTCHA, MFA) to sensitive flows by design.
- Write **abuse cases** and misuse tests alongside functional tests.

## Practice

- OWASP Juice Shop (many business-logic challenges)
- [PortSwigger business logic labs](https://portswigger.net/web-security/logic-flaws)

## Reference

- [OWASP A04:2021 Insecure Design](https://owasp.org/Top10/A04_2021-Insecure_Design/)
