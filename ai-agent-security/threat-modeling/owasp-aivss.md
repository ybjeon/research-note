# AIVSS Scoring System For OWASP Agentic AI Core Security Risks v0.8

Source: https://aivss.owasp.org/assets/publications/AIVSS%20Scoring%20System%20For%20OWASP%20Agentic%20AI%20Core%20Security%20Risks%20v0.8.pdf  
Date: 2026-07-07

## Overview
A v0.8 report jointly published by OWASP AIVSS (Agentic AI Vulnerability Scoring System), AIUC-1, OWASP AI Exchange, and OWASP Citizen Development Top 10. Composed of Part 1, which defines the 10 core security risks of Agentic AI systems, and Part 2, which covers the AIVSS-Agentic scoring methodology for quantitatively scoring those risks.

## Key Points
- Adopts a practical definition of Agentic AI: systems that autonomously set and pursue goals, use tools, and act using memory and context.
- CSA MAESTRO (a 7-layer architecture) is used to structurally identify where risk arises within a system, and AIVSS scores that risk.
- The core concept is **Agentic Risk Amplification**: an agent's capabilities such as autonomy, tool use, and memory act as a "force multiplier" for existing technical vulnerabilities (CVSS).
- Final score (AIVSS) = technical severity (CVSS_Base) + Agentic Uplift (AARS), with a Mitigation Factor then applied.
- Each of the 10 risks is provided with an example attack scenario and a CVSS v4.0-based scoring example.

## Details

### Part 1. OWASP Agentic AI Core Security Risks (10 categories, in order of severity)

1. **Agentic AI Tool Misuse**: Malfunctions arising from an agent's interaction with tools/APIs. Key risks include tool squatting (registering fake tools), insecure tool interfaces, misinterpretation of tool output, and lack of monitoring/kill switch. Examples: malicious code execution via tool, backdoor injection using an MCP server.
2. **Agent Access Control Violation**: An agent operates beyond its authorized scope. Includes permission escalation, role inheritance exploitation, credential/token mismanagement, and the confused deputy pattern. Example: GitHub Copilot's EchoLeak (zero-click data exfiltration).
3. **Agent Cascading Failures**: The compromise of one agent spreads across multiple connected systems. Includes cross-system exploitation, lateral movement, and hallucination propagation. Frequently overlaps with Tool Misuse, Goal Manipulation, and Access Control Violation.
4. **Agent Orchestration and Multi-Agent Exploitation**: Attacks targeting the communication/coordination mechanisms between multiple agents. Includes inter-agent communication exploitation, shared knowledge (RAG/memory) poisoning, session fixation/replay, and rogue autonomy.
5. **Agent Identity Impersonation**: An agent impersonates another agent or a human (or vice versa). Includes deepfake-based CEO fraud, agent-in-the-middle attacks, abuse of shared identity pools, and attribution gaps due to the absence of DID/VC.
6. **Agent Memory and Context Manipulation**: Corruption of an agent's stored context/memory. Includes context amnesia, cross-session leakage, cross-user contamination, RAG data poisoning, and missing TTL. Variance in "cognitive resilience" across agents affects susceptibility.
7. **Insecure Agent Critical Systems Interaction**: Arises when an agent interacts with physical infrastructure, IoT, or critical systems. Includes SSRF, CI/CD pipeline tampering, and agent misconfiguration exploitation. Examples: sabotage of a water treatment facility, physical intrusion into a data center.
8. **Agent Supply Chain and Dependency Risk**: Contamination of an agent's components such as pre-trained models, libraries, and third-party tools/MCP servers. Includes package hallucination (attackers registering non-existent package names) and capability creep (a vendor's silent expansion of privileges).
9. **Agent Untraceability**: A state in which the sequence, identity, and authorization of an agent's actions cannot be traced. Dynamic role inheritance and the lack of correlation across distributed logs create a "forensic black hole." Also includes tampering with explainability artifacts (SHAP/LIME, etc.).
10. **Agent Goal and Instruction Manipulation**: Subversion of an agent's core goals/decision logic via prompt injection, etc. Includes semantic ambiguity exploitation, direct/indirect instruction injection, dynamic goal steering, and resource exhaustion via goal looping.

### Part 2. AIVSS-Agentic Scoring System and Application

**Theoretical foundation**: While traditional vulnerabilities have a static impact ceiling, an agent's autonomy, tool use, and memory persistence amplify the impact scope of a technical flaw (a "force multiplier"). Three factors justify this amplification: core agency, environmental permeability, and probabilistic behavior.

**10 Risk Amplification Factors** (each scored 0.0/0.5/1.0):
1. Autonomy – ability to execute actions without human approval
2. Tools – scope and privilege of external tool/API control
3. Language – how directly natural language drives control logic
4. Context – breadth of environmental signal/data utilization
5. Non-Determinism – variance in output for identical input
6. Opacity – lack of visibility/auditability into internal logic
7. Persistence – ability to retain memory/state across sessions
8. Identity – ability to assume different roles/permissions at runtime
9. Multi-Agent – coordination with/dependency on other autonomous agents
10. Self-Modification – ability to alter its own code/prompts/tool configuration

**Calculation procedure**:
- `Factor_Sum` = sum of the 10 factor scores (maximum 10.0)
- `AARS (Agentic Uplift) = (10 - CVSS_Base) * (Factor_Sum / 10) * ThM`
  - ThM (Threat Multiplier): Attacked=1.00, PoC=0.97 (default), Unreported=0.50
- `AIVSS_raw = (CVSS_Base + AARS) * Mitigation_Factor`
  - Mitigation_Factor: No/Weak=1.00 (default), Partial=0.83, Strong=0.67 (floor)
- `AIVSS = RoundHalfUp(AIVSS_raw, 1)`
- CVSS input must be v4.0-based (must not mix with v3.1). The Subsequent System Impact (SC/SI/SA) metric is especially important in agentic scenarios.

**Severity Bands**: Critical (9.0-10.0), High (7.0-8.9), Medium (4.0-6.9), Low (0.1-3.9). Even with a low CVSS baseline, agentic amplification can substantially raise the final score (e.g., Goal Manipulation CVSS 2.1 → AIVSS 7.1). In such cases, architectural constraints (limiting autonomy/tool scope/memory retention) are more effective remediation than patching the vulnerability itself.

**Summary of scoring examples for the 10 risks** (CVSS_Base → AIVSS, Severity):
- Tool Misuse: 9.4 → 9.9 (Critical)
- Access Control Violation: 8.7 → 9.7 (Critical)
- Cascading Failures: 7.1 → 9.4 (Critical)
- Orchestration Exploitation: 9.4 → 10.0 (Critical)
- Identity Impersonation: 7.4 → 9.3 (Critical)
- Memory Manipulation: 5.8 → 8.9 (High)
- Critical Systems Interaction: 6.9 → 9.2 (Critical)
- Supply Chain Risk: 9.3 → 9.7 (Critical)
- Untraceability: 5.3 → 8.3 (High)
- Goal Manipulation: 2.1 → 7.1 (High)

**Implementation Guide**: Organizations should have system architecture understanding, expertise in agentic AI concepts, and AI/ML security knowledge as prerequisites, and assign roles such as AI Security Lead/Assessor, Agent Developers, SecOps/GRC, Risk Management/Compliance Officer, CISO, and AI Reliability & Policy Engineer to conduct the assessment. Reassessment is required upon architectural changes, shifts in the threat landscape, or scheduled review cycles. An AI Governance Board and AI Risk Classification Committee are recommended for release gate/approval mechanisms. Can be integrated with existing risk frameworks such as NIST AI RMF, ISO/IEC 27001/27002, and ISO/IEC 23894.

**Appendices**: JSON schema (Appendix A), mapping to the OWASP GenAI/LLM Agentic AI Top 10 2026 (Appendix B), CSA MAESTRO layer mapping (Appendix C), and December 2025 contributor survey findings (Appendix D) — in the survey, Agent Access Control Violation was ranked as the most severe risk, while Agent Untraceability and Cascading Failures were ranked relatively lower (interpreted as reflecting their nature as downstream/compounding risks).

## Conclusion
- The security risk of Agentic AI systems can be amplified far beyond that of traditional technical vulnerabilities due to an agent's autonomy, tool access, and memory persistence.
- AIVSS quantifies this by using CVSS as a baseline and applying an uplift based on 10 agentic factors.
- When a high uplift is observed, architectural constraints (reducing autonomy, limiting tool scope, memory isolation, strengthening human oversight) should be prioritized over patching.
- This document is a living document, to be revised through continuous updates and community feedback going forward.

## Reference
- [OWASP AIVSS Project](https://aivss.owasp.org)
- [AIVSS-AIUC-1 Crosswalk](https://aivss.owasp.org/aiuc-crosswalk/index.html)
- [CSA MAESTRO Framework](https://cloudsecurityalliance.org/blog/2025/02/06/agentic-ai-threat-modeling-framework-maestro)
- [CVSS v4.0 Specification, FIRST.org](https://www.first.org/cvss/v4.0/specification-document)
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
- [MITRE ATLAS](https://atlas.mitre.org/)
- [OWASP Agentic AI Top 10 for 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)
- [OWASP MAS Threat Modeling](https://genai.owasp.org/resource/multi-agentic-system-threat-modeling-guide-v1-0/)
- [CSA and OWASP AI Exchange Agentic AI Red Teaming Guide](https://cloudsecurityalliance.org/artifacts/agentic-ai-red-teaming-guide)
- [Digital Identity Rights Framework (DIRF)](https://cloudsecurityalliance.org/blog/2025/08/27/introducing-dirf-a-comprehensive-framework-for-protecting-digital-identities-in-agentic-ai-systems)
