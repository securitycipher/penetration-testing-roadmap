# Agentic AI Security

An **AI agent** is an LLM that can plan, remember, and take actions through tools - not just answer questions. Multi-agent systems chain several of these together. Giving a model the ability to *act* (run code, call APIs, move money, edit files) massively raises the stakes: a prompt injection is no longer a weird reply, it is an unauthorized action. This is now its own discipline, covered by the **OWASP Top 10 for Agentic Applications (2026)** and the **MAESTRO** threat-modeling methodology.

## Why agents are harder to secure

- They act autonomously, so a single bad decision cascades into real-world effects.
- They hold **memory**, which can be poisoned to influence future behavior.
- They use **tools** (often via [MCP](MCP%20Security.md)), inheriting every tool's blast radius.
- Multiple agents talk to each other, creating agent-to-agent trust and spoofing problems.

## Core threats (OWASP Agentic / ASI taxonomy)

- **T1 Memory Poisoning** - inject false data into short- or long-term memory to alter later decisions.
- **T2 Tool Misuse** - trick the agent into abusing its legitimate tools (agent hijacking).
- **T3 Privilege Compromise** - abuse role inheritance or misconfigured permissions to act beyond scope.
- **T4 Resource Overload** - exhaust compute/budget via expensive agent loops (see [Unbounded Consumption](Unbounded%20Consumption.md)).
- **T5 Cascading Hallucinations** - false output propagates through a chain and corrupts decisions.
- **T6 Intent Breaking / Goal Manipulation** - subvert the agent's planning and objectives.
- **T7 Misaligned / Deceptive Behavior**, **T8 Repudiation** (no audit trail), **T9 Identity Spoofing**.
- **T10 Overwhelming Human-in-the-Loop** - flood approvals so humans rubber-stamp actions.
- **T11 Remote Code Execution**, **T12 Agent Communication Poisoning**, **T13 Rogue Agents**, **T14/T15 Human attacks and trust manipulation**.

## MAESTRO threat modeling

MAESTRO extends STRIDE for agents by analyzing seven architectural layers, then the cross-layer threats that emerge between them:

1. Foundation model
2. Data operations (RAG, memory, embeddings)
3. Agent framework
4. Infrastructure
5. Evaluation and observability
6. Security and compliance
7. Ecosystem (other agents, marketplaces)

The point is that many agent attacks (for example code injection generated at the model layer but executed at the infra layer) cannot be fixed at a single layer.

## How to test agentic systems

1. Map the agent: what tools, memory, and other agents can it reach, and with what privileges?
2. Try [indirect prompt injection](Prompt%20Injection.md) through any data it ingests (docs, web, tool output).
3. Poison its memory and see if it changes future actions.
4. Attempt to trigger destructive tool calls without human approval.
5. Drive it into expensive loops; test for a hard cap on steps and spend.
6. In multi-agent setups, spoof or poison agent-to-agent messages.

## Mitigation

- **Least privilege** per agent and per tool; no cross-agent privilege delegation unless explicit.
- **Human approval** and per-action authentication for sensitive/destructive operations.
- **Sandbox execution** with egress controls; validate untrusted planner/tool output.
- **Memory isolation and validation**; session separation.
- **Usage ceilings** (steps, tool calls, budget) and rate limiting.
- **Immutable audit logging** of every tool call and decision for forensics.
- Zero-trust between agents: verify every agent-to-agent interaction.

## Related

- [MCP Security](MCP%20Security.md), [AI Red Teaming](AI%20Red%20Teaming.md)
- [Excessive Agency](Excessive%20Agency.md), [Prompt Injection](Prompt%20Injection.md)

## Resources

- [OWASP Top 10 for Agentic Applications (2026)](https://genai.owasp.org/)
- [OWASP Agentic AI - Threats and Mitigations](https://genai.owasp.org/)
- [MAESTRO threat modeling](https://agentic-threat-modeling.github.io/MAESTRO/)
