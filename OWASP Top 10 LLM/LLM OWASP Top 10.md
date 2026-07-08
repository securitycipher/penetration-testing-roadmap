Large Language Models (LLMs) have become a cornerstone of artificial intelligence, revolutionizing how we interact with machines. Their ability to process and generate human-quality text unlocks a vast array of functionalities, from streamlining customer service to composing creative content. However, amidst their remarkable capabilities lies a hidden threat landscape.  The LLM OWASP Top 10 addresses this critical need by identifying the ten most critical security vulnerabilities specific to LLMs. Developed by the Open Web Application Security Project (OWASP), this list serves as a comprehensive guide for mitigating risks and ensuring the secure deployment of LLMs.

# Understanding the Landscape: Why a Dedicated Top 10 for LLMs?

Traditional security practices often fall short when addressing LLM vulnerabilities. The LLM OWASP Top 10 focuses on the unique challenges posed by these models, encompassing aspects like:

- Data-Driven Nature: LLMs rely on massive datasets for training. Vulnerabilities within this data, such as bias or poisoning, can have a significant impact on the model's outputs.
- Complexity and Opacity: The inner workings of complex LLMs can be opaque. This lack of transparency makes it difficult to identify potential vulnerabilities or predict unintended behaviors.
- Integration with External Systems: LLMs often integrate with third-party plugins or applications. Weaknesses within these systems can create entry points for attackers to exploit the LLM itself.
## The Ten Critical Threats: Unveiling the LLM OWASP Top 10

The LLM OWASP Top 10 categorizes vulnerabilities into distinct areas, providing a roadmap for mitigating risks:

- LLM01: Prompt Injection: Malicious actors can manipulate prompts to exploit the LLM and generate unintended outputs, potentially leading to unauthorized access or data breaches.
- LLM02: Insecure Output Handling: Uncritical acceptance of LLM outputs, without proper validation, can expose downstream systems to vulnerabilities like code execution and data breaches.
- LLM03: Training Data Poisoning: Deliberate injection of biased or corrupted data into the training set can compromise the LLM's performance and reliability.
- LLM04: Model Denial of Service (DoS): Attackers can overload the LLM with complex or nonsensical prompts, exhausting resources and rendering the model unavailable for legitimate users.
- LLM05: Supply Chain Vulnerabilities: Weaknesses within the data sources, pre-trained models, or third-party plugins integrated with the LLM can create hidden security risks.
- LLM06: Sensitive Information Disclosure: LLMs can inadvertently reveal confidential information through outputs, even if not directly trained on such data, due to inference or data leakage during training.
- LLM07: Insecure Plugin Design: Plugins that lack proper security features can become entry points for attackers to exploit the LLM system.
- LLM08: Excessive Agency: Granting LLMs too much autonomy in decision-making processes can lead to unintended consequences or ethical dilemmas.
- LLM09: Overreliance: Blindly accepting LLM outputs without critical analysis can perpetuate biases, lead to errors in judgment, and diminish human expertise.
- LLM10: Model Theft: The proprietary LLM model itself can be compromised and its intellectual property stolen, potentially leading to unauthorized use or replication.

## Quick reference (detailed guides in this folder)

| ID | Risk | Guide |
|----|------|-------|
| LLM01 | Prompt Injection | [Prompt Injection](Prompt%20Injection.md) |
| LLM02 | Insecure Output Handling | [Insecure Output Handling](Insecure%20Output%20Handling.md) |
| LLM03 | Training Data Poisoning | [Training Data Poisoning](Training%20Data%20Poisoning.md) |
| LLM04 | Model Denial of Service | [Model Denial of Service](Model%20Denial%20of%20Service.md) |
| LLM05 | Supply Chain Vulnerabilities | [Supply Chain Vulnerabilities](Supply%20Chain%20Vulnerabilities.md) |
| LLM06 | Sensitive Information Disclosure | [Sensitive Information Disclosure](Sensitive%20Information%20Disclosure.md) |
| LLM07 | Insecure Plugin Design | [Insecure Plugin Design](Insecure%20Plugin%20Design.md) |
| LLM08 | Excessive Agency | [Excessive Agency](Excessive%20Agency.md) |
| LLM09 | Overreliance | [Overreliance](Overreliance.md) |
| LLM10 | Model Theft | [Model Theft](Model%20Theft.md) |

## Note on versions (2023 vs 2025)

This list is the **2023** edition. The **2025** update reworks several items - notably splitting out **System Prompt Leakage**, **Vector/Embedding Weaknesses** (RAG), **Misinformation**, and renaming Model DoS to **Unbounded Consumption**. Prompt Injection remains #1. Expect RAG/agent-specific risks to grow in importance.

## Tools for LLM testing

- [garak](https://github.com/leondz/garak) - LLM vulnerability scanner
- [PyRIT](https://github.com/Azure/PyRIT) - Microsoft's AI red-teaming toolkit
- [Damn Vulnerable LLM Agent](https://github.com/WithSecureLabs/damn-vulnerable-llm-agent), Gandalf (Lakera) for practice

## Beyond the List: Building a Secure LLM Ecosystem

The LLM OWASP Top 10 serves as a valuable starting point, but securing LLMs requires a holistic approach:

- Security by Design: Integrate security considerations throughout the LLM lifecycle, from initial design to deployment and ongoing maintenance.
- Data Security and Privacy: Implement robust data security and privacy practices to ensure the integrity and confidentiality of training data.
- Continuous Monitoring and Improvement: Continuously monitor LLM behavior and update security measures as needed to address evolving threats.
- Collaboration and Knowledge Sharing: Foster collaboration between developers, security professionals, and users to share best practices and address emerging vulnerabilities.


Securing LLMs is not the sole responsibility of developers or security experts.  It necessitates a shared commitment from organizations, users, and the broader AI community. By adopting the LLM OWASP Top 10 as a framework, prioritizing responsible development practices, and fostering a culture of security awareness, we can ensure that LLMs contribute to progress without compromising security.  Ultimately, a collaborative effort focused on mitigation, education, and continuous improvement is paramount in building trust and paving the way for a future where LLMs are powerful tools for good.
