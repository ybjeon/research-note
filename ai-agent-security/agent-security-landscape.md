# AI Agent Security Landscape

Updated at 2026-06-25


## Overview

주요 AI agent 플랫폼 31개의 공식 보안 문서를 분석한 비교 레포트. 각 플랫폼이 다루는 위협 유형과 제공하는 guardrail 접근 방식을 정리한다.


**분석 대상 플랫폼 분류**

- **Cloud Provider Platforms**: Amazon Bedrock, Google ADK, IBM watsonx
- **LLM API / AI Assistants**: OpenAI Agents SDK, OpenAI Codex, Anthropic Claude API/Code
- **Microsoft Ecosystem**: Agent Framework, Foundry Agent Service, AI Security Guidance, AutoGen
- **Open Source Frameworks**: LangChain/LangGraph, Deep Agents, CrewAI
- **Developer Tools**: GitHub Copilot, Apple App Intents/Siri
- **Enterprise Platforms**: Salesforce Agentforce
- **Security-focused Tools**: NVIDIA NeMo, Meta LlamaFirewall, Meta PurpleLlama, MCP
- **Niche / Community Tools**: OpenClaw, ZeroClaw
- **Security Standards / Guidelines**: OWASP Top 10 for Agentic Applications (2026), MITRE ATLAS v5.1, NIST AI RMF, CSA MAESTRO

**공통 위협 패턴**

- Prompt injection(직접/간접) 이 거의 모든 플랫폼의 1순위 위협
- Indirect prompt injection(외부 콘텐츠 경유)은 agent 특화 위협으로 부각
- Jailbreak는 LLM API/foundation model 레이어에서 집중 다룸
- 데이터 유출(credential, PII)은 실행 환경 격리가 없는 플랫폼에서 주요 위협
- MCP 도입 이후 tool metadata 기반 injection 등 신종 벡터 등장

---

## Platform Summary Table (플랫폼 요약)

| Platform | Category | 핵심 위협 | 대표 Guardrail | 주요 특징 |
|---|---|---|---|---|
| OpenAI Agents SDK | LLM API | Prompt injection, Data leakage | Structured output, Tool approval, Guardrail library | openai-guardrails Python 라이브러리 제공 |
| OpenAI Codex | Coding Agent | Prompt injection, Data exfiltration, Malware | Sandbox (Seatbelt/bwrap), Approval policy, Domain allowlist | 4단계 Approval preset (Auto/Read-Only/Untrusted/Auto Review) |
| Google ADK | Cloud Framework | Prompt injection, Indirect PI, PII exfiltration | Model Armor, Gemini content filter, Agent-Auth/User-Auth | "Secure Sandwich" pattern + VPC-SC + Model Armor |
| MS Agent Framework | Open Framework | Indirect PI, XSS/SQLi via output, Hallucination | Input allow-listing, Parameterized query, High-risk tool gate | 신뢰 경계(trust boundary) 기반 공동 책임 모델 |
| MS Foundry Agent Service | Cloud Platform | User prompt attack, XPIA (cross-prompt injection) | Prompt Shields, Spotlighting, RBAC, Private networking | User prompt / Document 두 유형의 Prompt Shields 제공 |
| MS AI Security Guidance | Security Guidance | Indirect PI, Agent hijacking, Supply chain | Defense-in-depth 4계층, IFC, Critic agent | Zero Trust + SFI(Secure Future Initiative) 기반 |
| MS AutoGen / Magentic-One | Multi-agent Framework | Prompt injection (웹 경유), Unauthorized action | Docker isolation, Log monitoring, HITL | AutoDefense: 멀티 에이전트 jailbreak 방어 |
| LangChain / LangGraph | OSS Framework | PII leakage, Prompt injection, Business rule violation | Deterministic + model-based guardrail, HITL hooks | Before/after agent hook으로 미들웨어 패턴 지원 |
| Deep Agents (JS) | OSS Framework | Context injection, Network exfiltration, Secret exposure | Sandbox, Network proxy, HITL | "Sandbox as Tool" 아키텍처 |
| Deep Agents Code (Python) | OSS Framework | Shell command abuse, Runaway execution | Shell allowlist (-S), Execution budget (--max-turns), Remote sandbox | Remote sandbox provider (E2B, Modal 등) 연동 |
| CrewAI | OSS Framework | MCP prompt injection via metadata, DNS rebinding, Token passthrough | HTTPS, JSON schema validation, Least privilege, Rate limiting | Tool metadata만으로도 injection 가능한 MCP 위협 명시 |
| Amazon Bedrock Agents | Cloud Platform | Prompt injection, Indirect PI | Guardrails, Advanced Prompts pre-processing, System prompt | Shared Responsibility Model 기반; 앱 레벨 방어는 고객 책임 |
| Amazon Bedrock AgentCore | Cloud Platform | Memory poisoning, Prompt injection, IAM over-privilege | KMS 암호화, Input validation, IAM least-privilege | Memory store 오염(memory poisoning) 위협 특화 |
| Anthropic Claude API | LLM API | Jailbreak, Indirect PI via tool results | Harmlessness screen (Haiku), JSON encoding, Least privilege | tool_result 블록을 통한 비신뢰 콘텐츠 격리 |
| Anthropic Claude Code | Developer Tool | Prompt injection, MCP server risk, WebDAV risk | Permission architecture (read-only default), Sandboxed bash, Allowlist | Windows WebDAV deprecated 위험 명시 |
| GitHub Copilot Cloud Agent | Developer Tool | Prompt injection, Unauthorized push, Sensitive data access | CodeQL, Secret scanning, HITL, Org-level guardrail | Issue/댓글 경유 prompt injection 시나리오 명시 |
| GitHub Copilot Agents | Developer Tool | Hallucination, Vulnerable code, Bias | Responsible AI 6원칙, Per-agent-type control | CLI/SDK/Spark/Cloud 유형별 독립 보안 제어 |
| Salesforce Agentforce | Enterprise Platform | Prompt injection, Hallucination, Data leakage to LLM | Einstein Trust Layer, AUP + AI AUP + Model Containment | Zero-data retention 정책으로 제3자 LLM 데이터 보호 |
| Salesforce Prompt Injection | Enterprise Platform | Direct/Indirect PI, Instruction override, Data exfiltration | Einstein Trust Layer, Input validation, Audit logging | 부분 내용만 확인 가능 (일부 URL 접근 불가) |
| IBM watsonx Orchestrate ADK | Cloud Platform | Crescendo attack, Encoded instructions, Foreign language bypass | Red-teaming, Adversarial scenario simulation | 8개 공격 분류 taxonomy 제공 (crescendo, encoding 등) |
| IBM watsonx Guardrails | Cloud Platform | HAP (Hate, Abuse, Profanity), Harmful content | HAP detection (content moderation) | 문서 접근 불가 (HTTP 500) |
| MCP (Model Context Protocol) | Protocol / Standard | Confused Deputy, Token passthrough, SSRF, Session hijacking | Per-client consent, Token audience validation, Private IP block, Session binding | OAuth 2.0 기반 인가 플로우의 보안 취약점 상세 분류 |
| NVIDIA NeMo Guardrails | Security Tool | Jailbreak, Topic violation, PII exposure, Agentic injection | Nemotron Jailbreak Detect, Topic Control, Agentic Security Rail | YAML config 기반 파이프라인 전 단계 다층 방어 |
| NVIDIA NeMo Jailbreak | Security Tool | Jailbreak, GCG attack, Non-English bypass | Perplexity heuristic, Model-based detector (Arctic + Random Forest) | GCG 공격 49/50 탐지; 지연 수치 공개 |
| Meta LlamaFirewall | Security Tool | Prompt injection, Indirect PI, Jailbreak, Code generation | PromptGuard 2, AlignmentCheck, CodeShield, Custom scanners | 실시간 chain-of-thought 감사 (AlignmentCheck scanner) |
| Meta PurpleLlama / Prompt Guard | Security Tool | Prompt injection, Jailbreak, Insecure code, Visual PI | Llama Guard 3, PromptGuard, CodeShield, CyberSecEval | 9개 공격 유형 커버; 멀티모달 visual PI 포함 |
| OpenClaw Gateway | Niche Tool | Prompt injection, Multi-user trust collapse, Credential exposure | Deny-by-default tool policy, Sandbox, Session isolation, Audit | Personal assistant 보안 모델 기반 |
| OpenClaw CLI | Niche Tool | Shared gateway risk, Small model vuln, Webhook token exposure | DM hardening, Suppression framework, Automated --fix remediation | 소형 모델 + web tool 조합 위험 명시 |
| ZeroClaw | Niche Tool | Privileged process risk, Env variable leakage | Autonomy levels (ReadOnly/Supervised/Full), Multi-backend sandbox | OS별 sandbox 자동 감지 (Landlock/bwrap/Docker/Seatbelt) |
| Apple Siri / Apple Intelligence | Mobile AI | Privacy risk (문서 내 위협 모델 미공개) | Private Cloud Compute, On-device processing | PCC: 클라우드 처리 시에도 서버 저장 없음 |
| Apple App Intents | Mobile AI | Unintended execution, Parameter leakage (문서 미확인) | On-device processing, PCC, User consent, Capability scoping | App Intents 기반 agent capability 범위 제한 |
| OWASP Top 10 Agentic | Security Standard | Agent Goal Hijack, Tool Misuse, Identity Abuse, Supply Chain, RCE, Memory Poisoning, Insecure Comms, Cascading Failures, Trust Exploitation, Rogue Agents | Least privilege, HITL, Sandbox, mTLS, Circuit breaker, SBOM/AIBOM, Behavioral monitoring | ASI01-ASI10; 10개 agentic AI 특화 위협 정의 (2025.12) |
| MITRE ATLAS | Security Framework | Adversarial ML, Data Poisoning, Model Extraction, Prompt Injection, Supply Chain | ATLAS Navigator, Arsenal (CALDERA), AI Incident Sharing, Threat Modeling | 16 tactics / 84 techniques; ATT&CK 기반 AI 특화 확장 (v5.4.0) |
| NIST AI RMF | Governance Framework | Trustworthiness gaps, AI bias, Supply chain risk, Generative AI risks | Govern / Map / Measure / Manage 4 functions, AI RMF Playbook | 자발적 지침; AI 전체 생애주기 리스크 관리 |
| CSA MAESTRO | Threat Modeling Framework | Cross-layer attacks, RAG poisoning, Supply chain, Lateral movement, Goal misalignment | 7-layer decomposition, Defense in depth, Adversarial training, Red teaming | 7계층 아키텍처 기반 agentic AI 위협 모델링 |

---

## Threat Coverage Matrix (위협 커버리지 매트릭스)

각 플랫폼이 공식 문서에서 명시적으로 다루는 위협 유형.

**범례**: o = 명시적 다룸, x = 언급 없음, △ = 간접 언급, - = 해당 없음(표준/프레임워크) 

**위협 커버리지**
| Platform | Prompt Injection | Indirect PI | Jailbreak | Data Exfiltration | Code Exec Risk | MCP Risk | Memory/State Poisoning | Hallucination |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| OpenAI Agents SDK | o | o | x | o | x | o | x | x |
| OpenAI Codex | o | o | x | o | o | x | x | x |
| Google ADK | o | o | x | o | o | x | x | o |
| MS Agent Framework | o | o | x | o | x | x | x | o |
| MS Foundry Agent Service | o | o | x | o | x | x | x | x |
| MS AI Security Guidance | o | o | x | o | x | x | x | x |
| MS AutoGen / Magentic-One | o | x | x | o | x | x | x | x |
| LangChain / LangGraph | o | x | x | o | x | x | x | o |
| Deep Agents (JS) | o | x | x | o | o | x | x | x |
| Deep Agents Code (Python) | x | x | x | x | o | x | x | x |
| CrewAI | o | x | x | o | x | o | x | x |
| Amazon Bedrock Agents | o | o | x | x | x | x | x | x |
| Amazon Bedrock AgentCore | o | x | x | x | x | x | o | x |
| Anthropic Claude API | o | o | o | x | x | x | x | x |
| Anthropic Claude Code | o | x | x | o | x | o | x | x |
| GitHub Copilot Cloud Agent | o | x | x | o | o | x | x | x |
| GitHub Copilot Agents | x | x | x | x | o | x | x | o |
| Salesforce Agentforce | o | x | x | o | x | x | x | o |
| Salesforce Prompt Injection | o | o | x | o | x | x | x | x |
| IBM watsonx Orchestrate | o | x | o | x | x | x | x | x |
| IBM watsonx Guardrails | x | x | x | x | x | x | x | x |
| MCP | o | x | x | o | x | o | x | x |
| NVIDIA NeMo Guardrails | x | x | o | o | x | x | x | o |
| NVIDIA NeMo Jailbreak | x | x | o | x | x | x | x | x |
| Meta LlamaFirewall | o | o | o | x | o | x | x | x |
| Meta PurpleLlama | o | o | o | x | o | x | x | x |
| OpenClaw Gateway | o | o | x | o | x | x | x | x |
| OpenClaw CLI | o | x | x | o | x | x | x | x |
| ZeroClaw | x | x | x | o | o | x | x | x |
| Apple Siri / Intelligence | x | x | x | △ | x | x | x | x |
| Apple App Intents | x | x | x | △ | x | x | x | x |
| OWASP Top 10 Agentic | o | o | x | x | o | x | o | x |
| MITRE ATLAS | o | o | x | o | △ | x | o | x |
| NIST AI RMF | △ | △ | △ | △ | △ | △ | △ | △ |
| CSA MAESTRO | o | △ | x | x | o | o | o | x |

**위협 커버리지 대응 방식**

각 플랫폼이 해당 위협에 어떤 방식으로 대응하는지 구체적인 메커니즘을 표시. `-`는 해당 위협을 다루지 않음.

| Platform | Prompt Injection | Indirect PI | Jailbreak | Data Exfiltration | Code Exec Risk | MCP Risk | Memory/State Poisoning | Hallucination |
|---|---|---|---|---|---|---|---|---|
| OpenAI Agents SDK | Guardrail library | Structured output | - | Tool approval | - | Tool approval | - | - |
| OpenAI Codex | Approval policy | Approval policy | - | Domain allowlist | Seatbelt/bwrap sandbox | - | - | - |
| Google ADK | Model Armor | Secure Sandwich pattern | - | VPC-SC | Sandbox | - | - | Gemini content filter |
| MS Agent Framework | Input allow-listing | IFC | - | Parameterized query | - | - | - | High-risk tool gate |
| MS Foundry Agent Service | Prompt Shields | Spotlighting + Document Shields | - | RBAC | - | - | - | - |
| MS AI Security Guidance | Defense-in-depth | IFC + Critic agent | - | Zero Trust | - | - | - | - |
| MS AutoGen / Magentic-One | Docker isolation | - | - | Log monitoring | - | - | - | - |
| LangChain / LangGraph | Deterministic + model-based guardrail | - | - | HITL hooks | - | - | - | Model-based guardrail |
| Deep Agents (JS) | Sandbox | - | - | Network proxy | Sandbox | - | - | - |
| Deep Agents Code (Python) | - | - | - | - | Shell allowlist (-S), Execution budget | - | - | - |
| CrewAI | JSON schema validation | - | - | Least privilege | - | JSON schema validation | - | - |
| Amazon Bedrock Agents | Guardrails, Pre-processing prompt | Advanced Prompts | - | - | - | - | - | - |
| Amazon Bedrock AgentCore | Input validation | - | - | - | - | - | KMS 암호화 | - |
| Anthropic Claude API | Harmlessness screen (Haiku) | JSON encoding | Harmlessness screen | - | - | - | - | - |
| Anthropic Claude Code | Allowlist | - | - | Permission architecture | - | Allowlist | - | - |
| GitHub Copilot Cloud Agent | CodeQL, Secret scanning | - | - | HITL, Org-level guardrail | CodeQL | - | - | - |
| GitHub Copilot Agents | - | - | - | - | Per-agent-type control | - | - | Responsible AI 6원칙 |
| Salesforce Agentforce | Einstein Trust Layer | - | - | Model Containment | - | - | - | AUP |
| Salesforce Prompt Injection | Prompt injection detection | Einstein Trust Layer | - | Audit logging | - | - | - | - |
| IBM watsonx Orchestrate | Red-teaming | - | Adversarial scenario simulation | - | - | - | - | - |
| IBM watsonx Guardrails | - | - | - | - | - | - | - | - |
| MCP | Per-client consent | - | - | Token audience validation | - | Session binding | - | - |
| NVIDIA NeMo Guardrails | - | - | Nemotron Jailbreak Detect | Topic Control | - | - | - | Content filter |
| NVIDIA NeMo Jailbreak | - | - | Perplexity heuristic + Random Forest | - | - | - | - | - |
| Meta LlamaFirewall | PromptGuard 2 | PromptGuard 2 | AlignmentCheck | - | CodeShield | - | - | - |
| Meta PurpleLlama | PromptGuard | Llama Guard 3 | PromptGuard | - | CodeShield | - | - | - |
| OpenClaw Gateway | Deny-by-default tool policy | Session isolation | - | Audit | - | - | - | - |
| OpenClaw CLI | DM hardening | - | - | Suppression framework | - | - | - | - |
| ZeroClaw | - | - | - | Autonomy levels | Multi-backend sandbox | - | - | - |
| Apple Siri / Intelligence | - | - | - | (Private Cloud Compute) | - | - | - | - |
| Apple App Intents | - | - | - | (On-device processing) | - | - | - | - |
| OWASP Top 10 Agentic | Input sanitization (ASI01) | Least privilege (ASI01) | - | - | Sandbox (ASI05) | - | Scan-before-write (ASI06) | - |
| MITRE ATLAS | AML.T0051 문서화 | ATLAS Navigator 매핑 | - | AML.TA0013 mitigation | (AML.TA0005) | - | AML.T0020 mitigation | - |
| NIST AI RMF | (Map function) | (Map function) | (Measure function) | (Map function) | (Map function) | (Map function) | (Map function) | (Measure function) |
| CSA MAESTRO | Layer 1/3 controls | Layer 1 controls | - | - | Layer 4 controls | Layer 7 controls | Layer 2 controls | - |

**위협 유형별 주요 대응 패턴**

| 위협 유형 | 주요 대응 Guardrail | 대표 구현 예시 |
|---|---|---|
| Prompt Injection | Input Filter, Content Filter Model | Bedrock Guardrails, MS Prompt Shields, NeMo PromptGuard, LlamaFirewall PromptGuard 2 |
| Indirect PI | Spotlighting, IFC (Information Flow Control), Context isolation | MS Spotlighting, Google "Secure Sandwich", MS IFC (Indirect injection Filter + Critic agent) |
| Jailbreak | Content Filter Model, Perplexity heuristic | Anthropic Harmlessness screen, NeMo Jailbreak Detect (Arctic + Random Forest), LlamaFirewall AlignmentCheck |
| Data Exfiltration | Access Control (least privilege), Sandbox (network isolation), Output validation | OpenAI Codex Domain allowlist, Claude Code network sandbox, ZeroClaw autonomy levels |
| Code Exec Risk | Sandbox (isolated execution), HITL (approval gate), Shell allowlist | OpenAI Codex Seatbelt/bwrap, Claude Code sandboxed bash, Deep Agents Code shell allowlist (-S) |
| MCP Risk | Input validation (JSON schema), Token audience validation, Per-client consent | CrewAI JSON schema validation, MCP OAuth token binding, MCP Private IP block |
| Memory/State Poisoning | Memory write validation, Memory segmentation, Provenance tracking | Bedrock AgentCore KMS 암호화, OWASP ASI06 scan-before-write 권고 |
| Hallucination | HITL (human verification), Structured output, Responsible AI evaluation | OpenAI Structured output, GitHub Copilot Responsible AI 6원칙, Google ADK content filter |

---

## Guardrail Approach Matrix (가드레일 접근 방식 매트릭스)

각 플랫폼이 공식 문서에서 제공하거나 권고하는 guardrail 구현 메커니즘. Security Standards / Guidelines 항목은 직접 구현 메커니즘을 제공하지 않으므로 `-`로 표시.

**범례**: o = 명시적 제공/권고, x = 언급 없음, △ = 간접 언급, - = 해당 없음(표준/프레임워크)

| Platform | Input Filter | Sandbox | HITL | Content Filter Model | Access Control | Audit Log | Security Eval / Red-team |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| OpenAI Agents SDK | o | x | o | o | x | x | o |
| OpenAI Codex | o | o | o | x | o | o | x |
| Google ADK | o | o | o | o | o | x | x |
| MS Agent Framework | o | x | o | x | o | x | x |
| MS Foundry Agent Service | o | x | x | o | o | o | x |
| MS AI Security Guidance | o | x | o | o | o | o | x |
| MS AutoGen / Magentic-One | x | o | o | x | o | o | x |
| LangChain / LangGraph | o | x | o | o | x | x | x |
| Deep Agents (JS) | x | o | o | x | o | x | x |
| Deep Agents Code (Python) | o | o | o | x | x | x | x |
| CrewAI | o | x | x | x | o | o | x |
| Amazon Bedrock Agents | o | x | x | o | x | x | x |
| Amazon Bedrock AgentCore | o | x | x | x | o | x | o |
| Anthropic Claude API | o | x | x | o | o | x | o |
| Anthropic Claude Code | o | o | o | x | o | x | x |
| GitHub Copilot Cloud Agent | o | x | o | o | o | o | x |
| GitHub Copilot Agents | x | x | o | x | x | x | x |
| Salesforce Agentforce | o | x | o | o | o | o | x |
| Salesforce Prompt Injection | o | x | x | o | x | o | x |
| IBM watsonx Orchestrate | x | x | x | x | x | x | o |
| IBM watsonx Guardrails | x | x | x | x | x | x | x |
| MCP | o | x | x | x | o | o | x |
| NVIDIA NeMo Guardrails | o | x | x | o | x | o | x |
| NVIDIA NeMo Jailbreak | o | x | x | o | x | x | x |
| Meta LlamaFirewall | o | x | x | o | x | o | x |
| Meta PurpleLlama | o | x | x | o | x | x | o |
| OpenClaw Gateway | o | o | o | x | o | o | x |
| OpenClaw CLI | o | x | x | x | o | o | o |
| ZeroClaw | x | o | x | x | o | o | x |
| Apple Siri / Intelligence | x | x | x | x | o | x | x |
| Apple App Intents | x | x | o | x | o | x | x |
| OWASP Top 10 Agentic | - | - | - | - | - | - | - |
| MITRE ATLAS | - | - | - | - | - | - | - |
| NIST AI RMF | - | - | - | - | - | - | - |
| CSA MAESTRO | - | - | - | - | - | - | - |

---

## Key Findings (주요 발견)

**Prompt injection 커버리지**
- 31개 플랫폼 중 22개(71%)가 direct prompt injection을 명시적으로 다룸
- Indirect prompt injection은 13개(42%)에서만 명시적으로 다룸 - agent 특화 위협임에도 상대적으로 커버리지 낮음
- OWASP Top 10 Agentic(ASI01 Agent Goal Hijack), MITRE ATLAS(AML.T0051)가 prompt injection을 핵심 위협으로 명시

**Sandbox 격리**
- Sandbox를 제공하는 플랫폼: OpenAI Codex, Google ADK, MS AutoGen, Deep Agents (JS/Python), Anthropic Claude Code, OpenClaw Gateway, ZeroClaw
- Cloud-hosted 플랫폼은 인프라 격리에 의존하고 별도 sandbox 문서를 제공하지 않는 경향
- OWASP Top 10 Agentic은 ASI05(RCE)에서 sandbox 실행 격리를 핵심 완화 전략으로 권고

**Human-in-the-Loop (HITL)**
- HITL을 공식 guardrail로 언급한 플랫폼: 17개(55%)
- 코딩 agent 계열(Codex, Deep Agents Code, Claude Code, GitHub Copilot)에서 특히 강조
- OWASP Top 10 Agentic은 ASI01/ASI09에서 고영향 작업 전 인간 승인을 명시적으로 요구

**보안 특화 플랫폼 vs 일반 프레임워크**
- NVIDIA NeMo, Meta LlamaFirewall, Meta PurpleLlama는 보안만을 목적으로 설계된 도구; 다른 플랫폼에 레이어로 추가 가능
- OWASP Top 10 Agentic / MITRE ATLAS / NIST AI RMF / CSA MAESTRO는 플랫폼 독립적인 표준/프레임워크로, 위 모든 플랫폼에 적용 가능
- 일반 프레임워크(LangChain, CrewAI, AutoGen)는 보안 기능이 상대적으로 제한적이며 외부 guardrail 통합을 권장

**문서 접근 불가 / 미확인 항목**
- IBM watsonx Guardrails: HTTP 500 오류로 내용 확인 불가
- Apple Siri, App Intents: JS-rendered 페이지로 위협 모델 문서 확인 불가
- Salesforce 블로그 일부: HTTP 403으로 접근 불가

---

## Sources
| 항목                                    | Threats / Risks 페이지                                                                                                                                                                                                                                                        | Security solutions / Guardrails 페이지                                                                                                                                                                                                                                             |
| ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| OpenAI Agents SDK / Agent Builder     | [https://developers.openai.com/api/docs/guides/agent-builder-safety](https://developers.openai.com/api/docs/guides/agent-builder-safety) ([OpenAI 개발자][1])                                                                                                                 | [https://openai.github.io/openai-guardrails-python/quickstart/](https://openai.github.io/openai-guardrails-python/quickstart/) ([OpenAI GitHub Pages][2])                                                                                                                       |
| OpenAI Codex / coding agent           | [https://developers.openai.com/codex/agent-approvals-security](https://developers.openai.com/codex/agent-approvals-security) ([OpenAI 개발자][3])                                                                                                                             | [https://developers.openai.com/codex/cloud/internet-access](https://developers.openai.com/codex/cloud/internet-access) ([OpenAI 개발자][4])                                                                                                                                        |
| Google ADK                            | [https://google.github.io/adk-docs/safety/](https://google.github.io/adk-docs/safety/) ([Google GitHub][5])                                                                                                                                                                | [https://codelabs.developers.google.com/secure-agent-modelarmor](https://codelabs.developers.google.com/secure-agent-modelarmor) ([Google Codelabs][6])                                                                                                                         |
| Microsoft Agent Framework             | [https://learn.microsoft.com/en-us/agent-framework/agents/safety](https://learn.microsoft.com/en-us/agent-framework/agents/safety) ([Microsoft Learn][7])                                                                                                                  | [https://learn.microsoft.com/en-us/agent-framework/agents/safety](https://learn.microsoft.com/en-us/agent-framework/agents/safety) ([Microsoft Learn][7])                                                                                                                       |
| Microsoft Foundry Agent Service       | [https://learn.microsoft.com/en-us/azure/foundry/agents/overview](https://learn.microsoft.com/en-us/azure/foundry/agents/overview) ([Microsoft Learn][8])                                                                                                                  | [https://learn.microsoft.com/en-us/azure/foundry/openai/concepts/content-filter-prompt-shields](https://learn.microsoft.com/en-us/azure/foundry/openai/concepts/content-filter-prompt-shields) ([Microsoft Learn][9])                                                           |
| Microsoft AI agent security guidance  | [https://learn.microsoft.com/en-us/security/zero-trust/sfi/secure-agentic-systems](https://learn.microsoft.com/en-us/security/zero-trust/sfi/secure-agentic-systems) ([Microsoft Learn][10])                                                                               | [https://learn.microsoft.com/en-us/security/zero-trust/sfi/defend-indirect-prompt-injection](https://learn.microsoft.com/en-us/security/zero-trust/sfi/defend-indirect-prompt-injection) ([Microsoft Learn][11])                                                                |
| Microsoft AutoGen / Magentic-One      | [https://microsoft.github.io/autogen/stable//reference/python/autogen_ext.teams.magentic_one.html](https://microsoft.github.io/autogen/stable//reference/python/autogen_ext.teams.magentic_one.html)                                                                       | [https://microsoft.github.io/autogen/0.2/blog/](https://microsoft.github.io/autogen/0.2/blog/) ([Microsoft GitHub][12])                                                                                                                                                         |
| LangChain / LangGraph                 | [https://docs.langchain.com/oss/python/langchain/guardrails](https://docs.langchain.com/oss/python/langchain/guardrails) ([LangChain Docs][13])                                                                                                                            | [https://docs.langchain.com/oss/python/langchain/guardrails](https://docs.langchain.com/oss/python/langchain/guardrails) ([LangChain Docs][13])                                                                                                                                 |
| Deep Agents                           | [https://docs.langchain.com/oss/javascript/deepagents/sandboxes](https://docs.langchain.com/oss/javascript/deepagents/sandboxes) ([LangChain Docs][14])                                                                                                                    | [https://docs.langchain.com/oss/javascript/deepagents/sandboxes](https://docs.langchain.com/oss/javascript/deepagents/sandboxes) ([LangChain Docs][14])                                                                                                                         |
| Deep Agents Code                      | [https://docs.langchain.com/oss/python/deepagents/code/overview](https://docs.langchain.com/oss/python/deepagents/code/overview) ([LangChain Docs][15])                                                                                                                    | [https://docs.langchain.com/oss/python/deepagents/code/overview](https://docs.langchain.com/oss/python/deepagents/code/overview) ([LangChain Docs][15])                                                                                                                         |
| CrewAI                                | [https://docs.crewai.com/en/mcp/security](https://docs.crewai.com/en/mcp/security) ([CrewAI Documentation][16])                                                                                                                                                            | [https://docs.crewai.com/en/mcp/security](https://docs.crewai.com/en/mcp/security) ([CrewAI Documentation][16])                                                                                                                                                                 |
| Amazon Bedrock Agents                 | [https://docs.aws.amazon.com/bedrock/latest/userguide/prompt-injection.html](https://docs.aws.amazon.com/bedrock/latest/userguide/prompt-injection.html) ([AWS Documentation][17])                                                                                         | [https://docs.aws.amazon.com/bedrock/latest/userguide/prompt-injection.html](https://docs.aws.amazon.com/bedrock/latest/userguide/prompt-injection.html) ([AWS Documentation][17])                                                                                              |
| Amazon Bedrock AgentCore              | [https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/best-practices.html](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/best-practices.html) ([AWS Documentation][18])                                                                           | [https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/best-practices.html](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/best-practices.html) ([AWS Documentation][18])                                                                                |
| Anthropic Claude API                  | [https://docs.anthropic.com/en/docs/test-and-evaluate/strengthen-guardrails/mitigate-jailbreaks](https://docs.anthropic.com/en/docs/test-and-evaluate/strengthen-guardrails/mitigate-jailbreaks) ([Claude API Docs][19])                                                   | [https://docs.anthropic.com/en/docs/test-and-evaluate/strengthen-guardrails/mitigate-jailbreaks](https://docs.anthropic.com/en/docs/test-and-evaluate/strengthen-guardrails/mitigate-jailbreaks) ([Claude API Docs][19])                                                        |
| Anthropic Claude Code                 | [https://code.claude.com/docs/en/security](https://code.claude.com/docs/en/security) ([Claude API Docs][20])                                                                                                                                                               | [https://code.claude.com/docs/en/security](https://code.claude.com/docs/en/security) ([Claude API Docs][20])                                                                                                                                                                    |
| GitHub Copilot cloud agent            | [https://docs.github.com/en/copilot/concepts/agents/cloud-agent/risks-and-mitigations](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/risks-and-mitigations) ([GitHub Docs][21])                                                                           | [https://docs.github.com/en/copilot/tutorials/cloud-agent/build-guardrails](https://docs.github.com/en/copilot/tutorials/cloud-agent/build-guardrails) ([GitHub Docs][22])                                                                                                      |
| GitHub Copilot Agents                 | [https://docs.github.com/en/copilot/responsible-use/agents](https://docs.github.com/en/copilot/responsible-use/agents) ([GitHub Docs][23])                                                                                                                                 | [https://docs.github.com/en/copilot/responsible-use/agents](https://docs.github.com/en/copilot/responsible-use/agents) ([GitHub Docs][23])                                                                                                                                      |
| Salesforce Agentforce                 | [https://trailhead.salesforce.com/content/learn/modules/trusted-agentic-ai/explore-agentforce-guardrails-and-trust-patterns](https://trailhead.salesforce.com/content/learn/modules/trusted-agentic-ai/explore-agentforce-guardrails-and-trust-patterns) ([Trailhead][24]) | [https://help.salesforce.com/s/articleView?id=xcloud.shr_safety_security_prompt_injection_detection.htm&language=en_US&type=5](https://help.salesforce.com/s/articleView?id=xcloud.shr_safety_security_prompt_injection_detection.htm&language=en_US&type=5) ([Salesforce][25]) |
| Salesforce prompt injection detection | [https://www.salesforce.com/blog/prompt-injection-detection/](https://www.salesforce.com/blog/prompt-injection-detection/) ([Salesforce][26])                                                                                                                              | [https://www.salesforce.com/blog/prompt-injection-detection/](https://www.salesforce.com/blog/prompt-injection-detection/) ([Salesforce][26])                                                                                                                                   |
| IBM watsonx Orchestrate ADK           | [https://developer.watson-orchestrate.ibm.com/evaluate/llm_vulnerability](https://developer.watson-orchestrate.ibm.com/evaluate/llm_vulnerability) ([IBM Watson Orchestrate][27])                                                                                          | [https://developer.ibm.com/tutorials/red-teaming-watsonx-orchestrate-ibm-bob/](https://developer.ibm.com/tutorials/red-teaming-watsonx-orchestrate-ibm-bob/) ([@ibmdeveloper][28])                                                                                              |
| IBM watsonx Guardrails                | [https://dataplatform.cloud.ibm.com/docs/content/wsj/analyze-data/fm-hap.html?context=wx](https://dataplatform.cloud.ibm.com/docs/content/wsj/analyze-data/fm-hap.html?context=wx) ([IBM Cloud Pak for Data][29])                                                          | [https://dataplatform.cloud.ibm.com/docs/content/wsj/analyze-data/fm-hap.html?context=wx](https://dataplatform.cloud.ibm.com/docs/content/wsj/analyze-data/fm-hap.html?context=wx) ([IBM Cloud Pak for Data][29])                                                               |
| Model Context Protocol, MCP           | [https://modelcontextprotocol.io/docs/tutorials/security/security_best_practices](https://modelcontextprotocol.io/docs/tutorials/security/security_best_practices) ([Model Context Protocol][30])                                                                          | [https://modelcontextprotocol.io/docs/tutorials/security/security_best_practices](https://modelcontextprotocol.io/docs/tutorials/security/security_best_practices) ([Model Context Protocol][30])                                                                               |
| NVIDIA NeMo Guardrails                | [https://docs.nvidia.com/nemo/guardrails/home](https://docs.nvidia.com/nemo/guardrails/home) ([NVIDIA Docs][31])                                                                                                                                                           | [https://docs.nvidia.com/nemo/guardrails/configure-guardrails/guardrail-catalog/agentic-security](https://docs.nvidia.com/nemo/guardrails/configure-guardrails/guardrail-catalog/agentic-security) ([NVIDIA Docs][32])                                                          |
| NVIDIA NeMo Jailbreak Protection      | [https://docs.nvidia.com/nemo/guardrails/configure-guardrails/guardrail-catalog/jailbreak-protection](https://docs.nvidia.com/nemo/guardrails/configure-guardrails/guardrail-catalog/jailbreak-protection) ([NVIDIA Docs][33])                                             | [https://docs.nvidia.com/nemo/guardrails/configure-guardrails/guardrail-catalog/jailbreak-protection](https://docs.nvidia.com/nemo/guardrails/configure-guardrails/guardrail-catalog/jailbreak-protection) ([NVIDIA Docs][33])                                                  |
| Meta LlamaFirewall                    | [https://ai.meta.com/research/publications/llamafirewall-an-open-source-guardrail-system-for-building-secure-ai-agents/](https://ai.meta.com/research/publications/llamafirewall-an-open-source-guardrail-system-for-building-secure-ai-agents/) ([메타 AI][34])             | [https://meta-llama.github.io/PurpleLlama/LlamaFirewall/docs/documentation/scanners/alignment-check](https://meta-llama.github.io/PurpleLlama/LlamaFirewall/docs/documentation/scanners/alignment-check) ([Meta Llama][35])                                                     |
| Meta Prompt Guard / PurpleLlama       | [https://github.com/meta-llama/PurpleLlama](https://github.com/meta-llama/PurpleLlama) ([GitHub][36])                                                                                                                                                                      | [https://github.com/meta-llama/PurpleLlama](https://github.com/meta-llama/PurpleLlama) ([GitHub][36])                                                                                                                                                                           |
| OpenClaw Gateway                      | [https://docs.openclaw.ai/gateway/security](https://docs.openclaw.ai/gateway/security) ([OpenClaw][37])                                                                                                                                                                    | [https://docs.openclaw.ai/gateway/security](https://docs.openclaw.ai/gateway/security) ([OpenClaw][37])                                                                                                                                                                         |
| OpenClaw CLI                          | [https://docs.openclaw.ai/cli/security](https://docs.openclaw.ai/cli/security) ([OpenClaw][38])                                                                                                                                                                            | [https://docs.openclaw.ai/cli/security](https://docs.openclaw.ai/cli/security) ([OpenClaw][38])                                                                                                                                                                                 |
| ZeroClaw                              | [https://github.com/zeroclaw-labs/zeroclaw/blob/master/docs/book/src/security/overview.md](https://github.com/zeroclaw-labs/zeroclaw/blob/master/docs/book/src/security/overview.md) ([GitHub][39])                                                                        | [https://github.com/zeroclaw-labs/zeroclaw/blob/master/docs/book/src/security/sandboxing.md](https://github.com/zeroclaw-labs/zeroclaw/blob/master/docs/book/src/security/sandboxing.md) ([GitHub][40])                                                                         |
| Apple Siri / Apple Intelligence       | [https://support.apple.com/en-eg/guide/iphone/iphe3f499e0e/ios](https://support.apple.com/en-eg/guide/iphone/iphe3f499e0e/ios) ([애플 지원][41])                                                                                                                               | [https://security.apple.com/documentation/private-cloud-compute](https://security.apple.com/documentation/private-cloud-compute) ([Apple Security Research][42])                                                                                                                |
| Apple App Intents                     | [https://developer.apple.com/documentation/appintents](https://developer.apple.com/documentation/appintents) ([Apple Developer][43])                                                                                                                                       | [https://developer.apple.com/apple-intelligence/](https://developer.apple.com/apple-intelligence/) ([Apple Developer][44])                                                                                                                                                      |
| OWASP Top 10 for Agentic Applications | [https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/) ([OWASP GenAI][45])                                                                                   | [https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/) ([OWASP GenAI][45])                                                                                       |
| MITRE ATLAS                           | [https://atlas.mitre.org/](https://atlas.mitre.org/) ([MITRE ATLAS][46])                                                                                                                                                                                                   | [https://atlas.mitre.org/](https://atlas.mitre.org/) ([MITRE ATLAS][46])                                                                                                                                                                                                       |
| NIST AI RMF                           | [https://www.nist.gov/itl/ai-risk-management-framework](https://www.nist.gov/itl/ai-risk-management-framework) ([NIST][47])                                                                                                                                                | [https://www.nist.gov/itl/ai-risk-management-framework](https://www.nist.gov/itl/ai-risk-management-framework) ([NIST][47])                                                                                                                                                    |
| CSA MAESTRO                           | [https://labs.cloudsecurityalliance.org/maestro/](https://labs.cloudsecurityalliance.org/maestro/) ([CSA][48])                                                                                                                                                              | [https://labs.cloudsecurityalliance.org/maestro/](https://labs.cloudsecurityalliance.org/maestro/) ([CSA][48])                                                                                                                                                                  |

## 


## References
- [OpenAI Agents SDK](articles/openai_agents_sdk_safety.md)
- [OpenAI Codex](articles/openai_codex_agent_safety.md)
- [Google ADK](articles/google_adk_safety.md)
- [Microsoft Agent Framework](articles/microsoft_agent_framework_safety.md)
- [Microsoft Foundry Agent Service](articles/microsoft_foundry_agent_service_safety.md)
- [Microsoft AI Agent Security Guidance](articles/microsoft_ai_agent_security_guidance.md)
- [Microsoft AutoGen / Magentic-One](articles/microsoft_autogen_magentic_one_safety.md)
- [LangChain / LangGraph](articles/langchain_langgraph_safety.md)
- [Deep Agents (JS)](articles/deep_agents_safety.md)
- [Deep Agents Code (Python)](articles/deep_agents_code_safety.md)
- [CrewAI](articles/crewai_safety.md)
- [Amazon Bedrock Agents](articles/amazon_bedrock_agents_safety.md)
- [Amazon Bedrock AgentCore](articles/amazon_bedrock_agentcore_safety.md)
- [Anthropic Claude API](articles/anthropic_claude_api_safety.md)
- [Anthropic Claude Code](articles/anthropic_claude_code_safety.md)
- [GitHub Copilot Cloud Agent](articles/github_copilot_cloud_agent_safety.md)
- [GitHub Copilot Agents](articles/github_copilot_agents_safety.md)
- [Salesforce Agentforce](articles/salesforce_agentforce_safety.md)
- [Salesforce Prompt Injection Detection](articles/salesforce_prompt_injection_safety.md)
- [IBM watsonx Orchestrate ADK](articles/ibm_watsonx_orchestrate_safety.md)
- [IBM watsonx Guardrails](articles/ibm_watsonx_guardrails_safety.md)
- [Model Context Protocol (MCP)](articles/mcp_security.md)
- [NVIDIA NeMo Guardrails](articles/nvidia_nemo_guardrails_safety.md)
- [NVIDIA NeMo Jailbreak Protection](articles/nvidia_nemo_jailbreak_protection.md)
- [Meta LlamaFirewall](articles/meta_llamafirewall_safety.md)
- [Meta PurpleLlama / Prompt Guard](articles/meta_prompt_guard_purple_llama.md)
- [OpenClaw Gateway](articles/openclaw_gateway_safety.md)
- [OpenClaw CLI](articles/openclaw_cli_safety.md)
- [ZeroClaw](articles/zeroclaw_safety.md)
- [Apple Siri / Apple Intelligence](articles/apple_siri_apple_intelligence_safety.md)
- [Apple App Intents](articles/apple_app_intents_safety.md)
- [OWASP Top 10 for Agentic Applications](articles/owasp_top10_agentic_applications.md)
- [MITRE ATLAS](articles/mitre_atlas.md)
- [NIST AI RMF](articles/nist_ai_rmf.md)
- [CSA MAESTRO](articles/csa_maestro.md)


[1]: https://developers.openai.com/api/docs/guides/agent-builder-safety?utm_source=chatgpt.com "Safety in building agents | OpenAI API"
[2]: https://openai.github.io/openai-guardrails-python/quickstart/?utm_source=chatgpt.com "Quickstart - OpenAI Guardrails Python"
[3]: https://developers.openai.com/codex/agent-approvals-security?utm_source=chatgpt.com "Agent approvals & security – Codex"
[4]: https://developers.openai.com/codex/cloud/internet-access?utm_source=chatgpt.com "Agent internet access – Codex web"
[5]: https://google.github.io/adk-docs/safety/?utm_source=chatgpt.com "Safety and Security for AI Agents - Agent Development Kit (ADK)"
[6]: https://codelabs.developers.google.com/secure-agent-modelarmor?utm_source=chatgpt.com "Building a secure agent system with Model Armor"
[7]: https://learn.microsoft.com/en-us/agent-framework/agents/safety "Agent Safety | Microsoft Learn"
[8]: https://learn.microsoft.com/en-us/azure/foundry/agents/overview?utm_source=chatgpt.com "What is Microsoft Foundry Agent Service?"
[9]: https://learn.microsoft.com/en-us/azure/foundry/openai/concepts/content-filter-prompt-shields?utm_source=chatgpt.com "Prompt Shields in Microsoft Foundry"
[10]: https://learn.microsoft.com/en-us/security/zero-trust/sfi/secure-agentic-systems?utm_source=chatgpt.com "Secure autonomous agentic AI systems"
[11]: https://learn.microsoft.com/en-us/security/zero-trust/sfi/defend-indirect-prompt-injection?utm_source=chatgpt.com "Defend against indirect prompt injection attacks"
[12]: https://microsoft.github.io/autogen/0.2/blog/?utm_source=chatgpt.com "Blog | AutoGen 0.2"
[13]: https://docs.langchain.com/oss/python/langchain/guardrails?utm_source=chatgpt.com "Guardrails - Docs by LangChain"
[14]: https://docs.langchain.com/oss/javascript/deepagents/sandboxes?utm_source=chatgpt.com "Sandboxes - Docs by LangChain"
[15]: https://docs.langchain.com/oss/python/deepagents/code/overview?utm_source=chatgpt.com "Deep Agents Code - Docs by LangChain"
[16]: https://docs.crewai.com/en/mcp/security?utm_source=chatgpt.com "MCP Security Considerations"
[17]: https://docs.aws.amazon.com/bedrock/latest/userguide/prompt-injection.html?utm_source=chatgpt.com "Prompt injection security - Amazon Bedrock"
[18]: https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/best-practices.html?utm_source=chatgpt.com "Best practices - Amazon Bedrock AgentCore"
[19]: https://docs.anthropic.com/en/docs/test-and-evaluate/strengthen-guardrails/mitigate-jailbreaks?utm_source=chatgpt.com "Mitigate jailbreaks and prompt injections - Claude API Docs"
[20]: https://docs.anthropic.com/en/docs/claude-code/security "Security - Claude Code Docs"
[21]: https://docs.github.com/en/copilot/concepts/agents/cloud-agent/risks-and-mitigations?utm_source=chatgpt.com "Risks and mitigations for GitHub Copilot cloud agent"
[22]: https://docs.github.com/en/copilot/tutorials/cloud-agent/build-guardrails?utm_source=chatgpt.com "Building guardrails for GitHub Copilot cloud agent"
[23]: https://docs.github.com/en/copilot/responsible-use/agents?utm_source=chatgpt.com "Application card: GitHub Copilot Agents"
[24]: https://trailhead.salesforce.com/content/learn/modules/trusted-agentic-ai/explore-agentforce-guardrails-and-trust-patterns?utm_source=chatgpt.com "Explore Agentforce Guardrails and Trust Patterns - Trailhead"
[25]: https://help.salesforce.com/s/articleView?id=xcloud.shr_safety_security_prompt_injection_detection.htm&language=en_US&type=5&utm_source=chatgpt.com "Safety and Security - Prompt Injection Detection Control"
[26]: https://www.salesforce.com/blog/prompt-injection-detection/?utm_source=chatgpt.com "Prompt Injection Detection: Securing AI Systems Against ..."
[27]: https://developer.watson-orchestrate.ibm.com/evaluate/llm_vulnerability?utm_source=chatgpt.com "LLM agent vulnerability testing"
[28]: https://developer.ibm.com/tutorials/red-teaming-watsonx-orchestrate-ibm-bob/?utm_source=chatgpt.com "Secure AI agents with red teaming using watsonx Orchestrate and ..."
[29]: https://dataplatform.cloud.ibm.com/docs/content/wsj/analyze-data/fm-hap.html?context=wx&utm_source=chatgpt.com "Filtering foundation model content with AI guardrails"
[30]: https://modelcontextprotocol.io/docs/tutorials/security/security_best_practices?utm_source=chatgpt.com "Security Best Practices"
[31]: https://docs.nvidia.com/nemo/guardrails/home?utm_source=chatgpt.com "NVIDIA NeMo Guardrails Library Developer Guide"
[32]: https://docs.nvidia.com/nemo/guardrails/configure-guardrails/guardrail-catalog/agentic-security?utm_source=chatgpt.com "Agentic Security | NVIDIA NeMo Guardrails Library ..."
[33]: https://docs.nvidia.com/nemo/guardrails/configure-guardrails/guardrail-catalog/jailbreak-protection?utm_source=chatgpt.com "Jailbreak Protection | NVIDIA NeMo Guardrails Library ..."
[34]: https://ai.meta.com/research/publications/llamafirewall-an-open-source-guardrail-system-for-building-secure-ai-agents/?utm_source=chatgpt.com "An open source guardrail system for building secure AI agents"
[35]: https://meta-llama.github.io/PurpleLlama/LlamaFirewall/docs/documentation/scanners/alignment-check?utm_source=chatgpt.com "AlignmentCheck | LlamaFirewall"
[36]: https://github.com/meta-llama/PurpleLlama?utm_source=chatgpt.com "meta-llama/PurpleLlama: Set of tools to assess and ..."
[37]: https://docs.openclaw.ai/gateway/security?utm_source=chatgpt.com "Security - OpenClaw"
[38]: https://docs.openclaw.ai/cli/security?utm_source=chatgpt.com "Security"
[39]: https://github.com/zeroclaw-labs/zeroclaw/blob/master/docs/book/src/security/overview.md "zeroclaw/docs/book/src/security/overview.md at master · zeroclaw-labs/zeroclaw · GitHub"
[40]: https://github.com/zeroclaw-labs/zeroclaw/blob/master/docs/book/src/security/sandboxing.md "zeroclaw/docs/book/src/security/sandboxing.md at master · zeroclaw-labs/zeroclaw · GitHub"
[41]: https://support.apple.com/en-eg/guide/iphone/iphe3f499e0e/ios?utm_source=chatgpt.com "Apple Intelligence and privacy on iPhone"
[42]: https://security.apple.com/documentation/private-cloud-compute?utm_source=chatgpt.com "Private Cloud Compute Security Guide | Documentation"
[43]: https://developer.apple.com/documentation/appintents?utm_source=chatgpt.com "App Intents | Apple Developer Documentation"
[44]: https://developer.apple.com/apple-intelligence/?utm_source=chatgpt.com "Apple Intelligence - Apple Developer"
[45]: https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/ "OWASP Top 10 for Agentic Applications 2026"
[46]: https://atlas.mitre.org/ "MITRE ATLAS™"
[47]: https://www.nist.gov/itl/ai-risk-management-framework "AI Risk Management Framework | NIST"
[48]: https://labs.cloudsecurityalliance.org/maestro/ "Welcome to MAESTRO - CSA"
