# MCP Security (Model Context Protocol)

The Model Context Protocol (MCP) is the emerging standard for connecting AI assistants and agents to external tools and data (file systems, databases, APIs, SaaS). It is powerful and now built into developer IDEs, which also makes it a fast-growing attack surface. If an agent can call tools, the tools - and the servers that host them - become part of your threat model.

## Why it is risky

An agent reads a tool's **description, schema, and parameters** to decide when and how to call it, then feeds the tool's **response** straight back into the model's context. Both of those channels are usually trusted blindly. The core problem is a trust gap: tool definitions are reviewed once at connect time, but responses hit the context at runtime with no equivalent check.

## Key attacks

- **Tool poisoning** - a malicious or compromised MCP server hides instructions inside a tool's description, schema, or response. When the agent reads them, it treats them as trusted input (indirect [prompt injection](Prompt%20Injection.md)) and may call privileged tools, leak data, or override its system prompt. OWASP lists this as #3 in its MCP Top 10.
- **Rug pull** - a legitimate, approved tool is silently swapped for a malicious version after you trusted it.
- **Confused deputy / privilege escalation** - an untrusted external tool's response triggers a trusted internal tool because they share the same privilege level.
- **IDE auto-execution** - several IDEs (Cursor, Claude Code, Gemini CLI, Copilot, Amazon Q) auto-run project-defined MCP servers with developer-level OS privileges and no sandboxing. A poisoned `.mcp`/config committed to a shared repo can compromise a whole team (for example CVE-2025-54136 "MCPoison").
- **Data exfiltration** - a benign-looking tool (a calculator, a summarizer) quietly makes outbound calls to steal secrets like SSH keys.

A published benchmark (MCPTox) measured an average tool-poisoning success rate around 36%, and much higher against some models - this is not theoretical.

## How to test MCP in an engagement

1. Enumerate connected MCP servers and read the **full** tool manifests, not the shortened UI summary.
2. Plant hidden instructions in a tool description/response and see if the agent obeys them.
3. Check whether an external tool's output can trigger a privileged internal tool.
4. Look for IDE auto-execution of repo-defined servers; try a poisoned config in a test repo.
5. Diff tool definitions over time to catch rug pulls.

## Mitigation

- **Allowlist approved MCP servers**; do not let users connect to arbitrary ones.
- **Pin and hash** tool definitions at approval time; alert on any change (treat config changes as code review).
- **Validate responses** against a strict schema; do not dump free-text tool output into context unchecked.
- **Isolate privileged tools** in a separate context external servers cannot reach; enforce least privilege at the tool-execution layer, not in the system prompt.
- **Sandbox** servers and apply egress controls; require explicit user approval for destructive or data-sharing actions.
- Microsoft and OWASP both classify MCP tool descriptions as **software supply-chain assets** - review them like production code.

## Related

- [Agentic AI Security](Agentic%20AI%20Security.md), [AI Red Teaming](AI%20Red%20Teaming.md)
- [Prompt Injection](Prompt%20Injection.md), [Supply Chain Vulnerabilities](Supply%20Chain%20Vulnerabilities.md)

## Resources

- [OWASP MCP Tool Poisoning](https://community.owasp.org/attacks/MCP_Tool_Poisoning)
- [OWASP GenAI: securely using third-party MCP servers](https://genai.owasp.org/)
