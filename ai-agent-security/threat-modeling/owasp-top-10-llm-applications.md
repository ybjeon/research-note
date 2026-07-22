# OWASP Top 10 for Large Language Model Applications

Source: https://owasp.org/www-project-top-10-for-large-language-model-applications/
Date: 2025-01-27
Version: 1.1

## Overview
This is the OWASP project page for the Top 10 for Large Language Model (LLM) Applications, which identifies the most critical security vulnerabilities in LLM applications. The project has since expanded into the broader OWASP GenAI Security Project, covering LLMs, agentic AI systems, and other generative AI technologies.

## Key Points
- The original Top 10 project has grown into the OWASP GenAI Security Project, a global open-source initiative covering security and safety risks of generative AI (LLMs, agentic AI, AI-driven apps).
- This v1.1 list predates the rise of autonomous, tool-using agents. For threats specific to multi-step, tool-calling agents (goal hijacking, tool misuse, rogue agents), see [OWASP Top 10 for Agentic Applications 2026](owasp-top10-agentic-applications.md).
- LLM07 (Insecure Plugin Design) and LLM08 (Excessive Agency) are the two categories most relevant to agentic systems, since plugin/tool use and autonomy are exactly what agents add on top of a plain LLM.
- The 10 risks span the full application lifecycle: input handling (LLM01), output handling (LLM02), training data and dependencies (LLM03, LLM05), infra availability (LLM04), data leakage (LLM06), and human trust in the model (LLM09, LLM10).
- Best used as a threat-modeling checklist for classic LLM integrations (chatbots, RAG, plugin-enabled apps) rather than fully autonomous multi-step agents, which the Agentic Top 10 covers separately.

## Details
### Top 10 List (Version 1.1)
- **LLM01: Prompt Injection** - Crafted inputs manipulate LLMs, leading to unauthorized access, data breaches, or compromised decision-making.
- **LLM02: Insecure Output Handling** - Failing to validate LLM outputs can enable downstream exploits, including code execution.
- **LLM03: Training Data Poisoning** - Tampered training data can impair models, causing responses that compromise security, accuracy, or ethics.
- **LLM04: Model Denial of Service** - Resource-heavy operations against LLMs can cause service disruption and increased costs.
- **LLM05: Supply Chain Vulnerabilities** - Compromised components, services, or datasets undermine system integrity, causing breaches or failures.
- **LLM06: Sensitive Information Disclosure** - Failing to protect against sensitive data leakage in outputs can cause legal issues or loss of competitive advantage.
- **LLM07: Insecure Plugin Design** - Plugins processing untrusted input with insufficient access control risk severe exploits like remote code execution.
- **LLM08: Excessive Agency** - Granting LLMs unchecked autonomy can cause unintended consequences, harming reliability, privacy, and trust.
- **LLM09: Overreliance** - Failing to critically assess LLM outputs can lead to compromised decisions, security vulnerabilities, and legal liability.
- **LLM10: Model Theft** - Unauthorized access to proprietary models risks theft, loss of competitive advantage, and sensitive information disclosure.

## Conclusion
The OWASP Top 10 for LLM Applications (v1.1) remains a foundational reference for the most critical LLM application security risks, but the project has since evolved into the broader OWASP GenAI Security Project, with the current, maintained Top 10 list now hosted at genai.owasp.org/llm-top-10.

## Reference
[1] [OWASP Top 10 for Large Language Model Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
[2] [OWASP GenAI Security Project](https://genai.owasp.org/)
[3] [Latest Top 10 for LLM Applications](https://genai.owasp.org/llm-top-10/)
