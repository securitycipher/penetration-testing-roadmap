# Unbounded Consumption (LLM10:2025)

Unbounded Consumption is the **2025** rename and expansion of the old "Model Denial of Service." It still covers resource-exhaustion attacks, but now explicitly includes **cost / wallet attacks** (running up the victim's inference bill) and **model extraction** through excessive querying.

## Why it matters

LLM inference is expensive and usually metered per token. An attacker who can send unlimited or oversized requests can exhaust capacity, spike the bill, or slowly steal the model's behavior by querying it at scale. Pay-per-use APIs make the financial angle a first-class risk.

## Attack scenarios

- **Denial of wallet:** flood the app with expensive, max-token requests to drive up API cost.
- **Resource exhaustion:** very long inputs, huge `max_tokens`, or recursive/agentic loops that never terminate.
- **Model extraction / distillation:** systematically query the model to replicate its outputs and train a clone (overlaps with [Model Theft](Model%20Theft.md)).
- **Amplification:** a single user request triggers many internal model calls (agents, chains) with no cap.

## Test payloads

```text
# Oversized / expensive requests
Send inputs near the context-window limit repeatedly.
Request maximum output length ("write a 50,000 word essay").

# Loop / amplification probes (agentic apps)
"Keep refining this answer forever until it is perfect."
Trigger a tool-using agent and watch how many calls one prompt causes.

# Extraction pattern
Automate thousands of varied prompts and capture outputs at scale.
```

## How to test an LLM app

1. Check for **rate limiting and quotas** per user, IP, and API key.
2. Confirm input length and `max_tokens` are capped server-side.
3. For agent/chain apps, look for a hard cap on internal calls and recursion depth.
4. Measure whether a single cheap request can cause disproportionate backend cost.

## Mitigation

- Enforce rate limits, request quotas, and per-user spend budgets.
- Cap input size, output tokens, and agent loop/recursion depth.
- Monitor spend and set billing alerts; throttle or block anomalies.
- Use queuing and timeouts so one client cannot starve others.

## Related

- [Model Theft](Model%20Theft.md) - extraction overlaps with this risk
- Earlier name: Model Denial of Service (2023)
