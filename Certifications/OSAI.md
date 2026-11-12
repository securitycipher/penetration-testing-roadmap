# OSAI / OSAI+ (OffSec AI Red Teamer)

OSAI is OffSec's hands-on **AI red teaming** certification - the AI-focused sibling of OSCP. It validates that you can attack a realistic, AI-enabled enterprise: not just prompt-hacking a chatbot, but chaining AI and traditional attack paths across a network. It is one of the first vendor exams built specifically around offensive AI security.

## Course and exam

- Course: **AI-300** (Advanced AI Red Teaming), modular and self-paced
- Exam: **24-hour**, fully **proctored**, hands-on red-team engagement, then 24 hours to submit the report
- Passing the exam awards both **OSAI** (does not expire) and **OSAI+** (expires after 3 years, kept current with continuing education)
- Course availability began March 31, 2026; the earliest exam slots opened July 15, 2026

## Exam environment

- Ten machines: two independent three-machine attack chains that converge on a shared **Domain Controller**, plus a standalone AI-focused host (two decoy machines carry no points)
- Scoring out of 100: AI-vector machines 15 points each, traditional-vector machines 10 each, the standalone AI host 15, and the DC flag 5
- You need **75 points to pass**, plus a report thorough enough to replicate every step

## What makes it different

- **Open-book, and using AI chatbots/LLMs is encouraged** - effective use of AI is part of the assessment
- No tool restrictions
- You must blend AI attacks (LLMs, multi-agent systems, RAG pipelines, embeddings, model infrastructure) with classic network/AD compromise

## What it covers

- Attacking LLM-backed apps (see [OWASP Top 10 LLM](../OWASP%20Top%2010%20LLM/LLM%20OWASP%20Top%2010.md))
- Multi-agent and [agentic AI](../OWASP%20Top%2010%20LLM/Agentic%20AI%20Security.md) systems and tool abuse
- RAG, embeddings, and [vector-store weaknesses](../OWASP%20Top%2010%20LLM/Vector%20and%20Embedding%20Weaknesses.md)
- AI infrastructure plus traditional [Active Directory](../Active%20Directory/Active%20Directory%20Basics.md) attack chains

## Who it is for

- Experienced pentesters and red teamers moving into AI security
- OSCP-level skills are strongly recommended before attempting

## How to prepare

- Work the AI-300 labs end to end
- Build AI attack intuition with the [AI Red Teaming](../OWASP%20Top%2010%20LLM/AI%20Red%20Teaming.md) guide and tools like garak, PyRIT, and promptfoo
- Keep your general offensive skills sharp - half the exam is traditional

## Resources

- [OffSec AI-300 / OSAI](https://www.offsec.com/courses/ai-300/)
- [OSAI Exam Guide](https://help.offsec.com/hc/en-us/articles/46593096734612-OSAI-Exam-Guide)

## Compare

- **OSAI** - OffSec AI red team (AI + traditional chains), proctored
- **HTB AI Red Teamer** - path-based AI attack cert, cheaper
- **SecOps Group AI/ML Pentester** - focused practical AI app exam
