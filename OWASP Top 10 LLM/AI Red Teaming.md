# AI Red Teaming

AI red teaming is the practical craft of attacking AI-enabled systems to find failures before adversaries do. It overlaps with classic pentesting but adds model-specific concerns: prompts, guardrails, RAG pipelines, agents, tools, and the training/inference infrastructure behind them. This guide is the methodology and toolset; the specific risk categories live in the [OWASP Top 10 LLM](LLM%20OWASP%20Top%2010.md), [MCP Security](MCP%20Security.md), and [Agentic AI Security](Agentic%20AI%20Security.md) pages.

## What you are actually testing

- The **model/guardrails** - jailbreaks, [prompt injection](Prompt%20Injection.md), unsafe outputs
- The **data layer** - RAG, [embeddings/vector stores](Vector%20and%20Embedding%20Weaknesses.md), training data
- The **agent layer** - tools, memory, [MCP](MCP%20Security.md), multi-agent flows
- The **app around it** - classic web/API bugs ([XSS](../Vulnerabilities/XSS.md), [IDOR](../Vulnerabilities/IDOR.md), authz) triggered via model output
- The **infrastructure** - model hosting, keys, pipelines, supply chain

Most real findings are at the boundaries: the model is tricked, and a normal app bug turns that into impact (model output causing XSS, a tool call causing RCE).

## A practical methodology

1. **Map the system.** What model, what tools, what data sources, what actions can it take, and with what privileges? Draw the trust boundaries.
2. **Define objectives.** Data exfiltration, unauthorized actions, guardrail bypass, cost abuse, or full host compromise.
3. **Attack the model.** Extract the [system prompt](System%20Prompt%20Leakage.md), jailbreak guardrails, test direct and indirect prompt injection.
4. **Attack the data.** Poison a document the RAG pipeline ingests; probe cross-tenant retrieval.
5. **Attack the agency.** Trigger tool calls, poison memory, chase destructive actions without approval.
6. **Chain to the app/infra.** Turn model output into real impact; pivot like a normal engagement.
7. **Measure and report.** Attacks are probabilistic - run probes many times, report success rates, and give concrete fixes.

## Tools

- **garak** - open-source LLM vulnerability scanner (jailbreaks, injection, leakage)
- **PyRIT** - Microsoft's AI red-teaming automation framework
- **promptfoo** - prompt testing and red-team evals in CI
- **Damn Vulnerable LLM Agent**, **Lakera Gandalf** - practice targets
- Classic tools still apply: [Burp Suite](../Tools/Burp%20Suite.md) for the surrounding app traffic

## Frameworks and references

- [OWASP Top 10 for LLM Applications](LLM%20OWASP%20Top%2010.md) and the **Agentic Applications Top 10**
- **MITRE ATLAS** - the adversarial ML attack knowledge base (the ATT&CK of AI)
- **MAESTRO** - layered agentic threat modeling (see [Agentic AI Security](Agentic%20AI%20Security.md))
- **NIST AI RMF** - risk management context for defenders

## Certifications

If you want to formalize this, see the [OSAI](../Certifications/OSAI.md) (OffSec AI Red Teamer) and the other [AI / ML security certs](../Certifications/AI-ML%20Pentester.md).

## Related

- [Prompt Injection](Prompt%20Injection.md), [MCP Security](MCP%20Security.md), [Agentic AI Security](Agentic%20AI%20Security.md)
