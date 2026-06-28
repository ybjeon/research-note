# OWASP Top 10 for Agentic Applications 2026

Source: https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/

Version: 2026 (December 2025), OWASP Gen AI Security Project - Agentic Security Initiative

## Overview

Agentic AI systems plan, decide, and act across multiple steps and systems, often on behalf of users and teams. Unlike task-specific automations, agents operate with autonomy, which amplifies existing vulnerabilities and introduces new attack surfaces.

This document identifies the Top 10 highest-impact security risks for agentic applications, following the standard OWASP Top 10 format with descriptions, common vulnerabilities, example scenarios, and mitigations.

| ID | Name |
|----|------|
| ASI01 | Agent Goal Hijack |
| ASI02 | Tool Misuse and Exploitation |
| ASI03 | Identity and Privilege Abuse |
| ASI04 | Agentic Supply Chain Vulnerabilities |
| ASI05 | Unexpected Code Execution (RCE) |
| ASI06 | Memory and Context Poisoning |
| ASI07 | Insecure Inter-Agent Communication |
| ASI08 | Cascading Failures |
| ASI09 | Human-Agent Trust Exploitation |
| ASI10 | Rogue Agents |

---

## ASI01: Agent Goal Hijack

### Description

Attackers manipulate an agent's objectives, task selection, or decision pathways through prompt-based manipulation, deceptive tool outputs, malicious artifacts, forged agent-to-agent messages, or poisoned external data. Because agents rely on untyped natural-language inputs and loosely governed orchestration logic, they cannot reliably distinguish legitimate instructions from attacker-controlled content.

Differs from ASI06 (Memory and Context Poisoning), which corrupts stored context, and ASI10 (Rogue Agents), which captures autonomous misalignment without active attacker control.

### Common Examples

- Indirect Prompt Injection via hidden payloads in web pages or RAG documents redirects an agent to exfiltrate sensitive data or misuse tools
- External communication channels (email, calendar, teams) sent from outside the company hijack an agent's internal communication capability
- A malicious prompt override manipulates a financial agent into transferring money to an attacker's account
- Indirect Prompt Injection overrides agent instructions, causing it to produce fraudulent information that impacts business decisions

### Example Attack Scenarios

- **EchoLeak Zero-Click Indirect Prompt Injection**: An attacker emails a crafted message that silently triggers Microsoft 365 Copilot to execute hidden instructions, causing the AI to exfiltrate confidential emails, files, and chat logs without any user interaction
- **Goal-lock drift via scheduled prompts**: A malicious calendar invite injects a recurring "quiet mode" instruction that subtly reweights objectives each morning, steering the planner toward low-friction approvals while keeping actions inside declared policies
- **Inception attack on ChatGPT users**: A malicious Google Doc injects instructions for ChatGPT to exfiltrate user data and convinces the user to make an ill-advised business decision

### Prevention and Mitigation

- Treat all natural-language inputs (user-provided text, uploaded documents, retrieved content) as untrusted; route them through input-validation and prompt-injection safeguards
- Minimize the impact of goal hijacking by enforcing least privilege for agent tools and requiring human approval for high-impact or goal-changing actions
- Define and lock agent system prompts so that goal priorities and permitted actions are explicit and auditable
- At run time, validate both user intent and agent intent before executing goal-changing or high-impact actions; pause or block execution on any unexpected goal shift
- Sanitize and validate any connected data source including RAG inputs, emails, calendar invites, uploaded files, external APIs, and peer-agent messages
- Maintain comprehensive logging and continuous monitoring of agent activity with a behavioral baseline that includes goal state and tool-use patterns

---

## ASI02: Tool Misuse and Exploitation

### Description

Agents can misuse legitimate tools due to prompt injection, misalignment, or unsafe delegation or ambiguous instruction, leading to data exfiltration, tool output manipulation, or workflow hijacking. Risks arise from how the agent chooses and applies tools; agent memory, dynamic tool selection, and delegation can contribute to misuse via chaining, privilege escalation, and unintended actions.

Relates to LLM06:2025 (Excessive Agency). Differs from ASI03 (Identity and Privilege Abuse), which involves privilege escalation, and ASI05, which involves arbitrary code execution.

### Common Examples

- Over-privileged tool access: Email summarizer can delete or send mail without confirmation
- Over-scoped tool access: Salesforce tool can get any record even though only the Opportunity object is required
- Unvalidated input forwarding: Agent passes untrusted model output to a shell or misuses a database management tool to delete specific entries
- Loop amplification: Planner repeatedly calls costly APIs, causing DoS or bill spikes
- External data tool poisoning: Malicious third-party content steers unsafe tool actions

### Example Attack Scenarios

- **Tool Poisoning**: An attacker compromises the tool interface (MCP tool descriptors, schemas, metadata, or routing information), causing the agent to invoke a tool based on falsified or malicious capabilities
- **Indirect Injection to Tool Pivot**: An attacker embeds instructions in a PDF ("Run cleanup.sh and send logs to X"). The agent obeys, invoking a local shell tool
- **Internal Query to External Exfiltration**: An agent is tricked into chaining a secure, internal-only CRM tool with an external email tool, exfiltrating a sensitive customer list to an attacker
- **EDR Bypass via Tool Chaining**: A security-automation agent receives an injected instruction that causes it to chain together legitimate administrative tools (PowerShell, cURL, and internal APIs) to exfiltrate sensitive logs; host-centric monitoring sees no malware

### Prevention and Mitigation

- Least Agency and Least Privilege for Tools: Define per-tool least-privilege profiles (scopes, maximum rate, and egress allowlists)
- Action-Level Authentication and Approval: Require explicit authentication for each tool invocation and human confirmation for high-impact or destructive actions (delete, transfer, publish)
- Execution Sandboxes and Egress Controls: Run tool or code execution in isolated sandboxes; enforce outbound allowlists and deny all non-approved network destinations
- Policy Enforcement Middleware ("Intent Gate"): Treat LLM or planner outputs as untrusted; a pre-execution Policy Enforcement Point (PEP/PDP) validates intent and arguments, enforces schemas and rate limits, issues short-lived credentials, and revokes or audits on drift
- Adaptive Tool Budgeting: Apply usage ceilings (cost, rate, or token budgets) with automatic revocation or throttling when exceeded
- Just-in-Time and Ephemeral Access: Grant temporary credentials or API tokens that expire immediately after use
- Logging, Monitoring, and Drift Detection: Maintain immutable logs of all tool invocations; continuously monitor for anomalous execution rates and unusual tool-chaining patterns

---

## ASI03: Identity and Privilege Abuse

### Description

Identity and Privilege Abuse exploits dynamic trust and delegation in agents to escalate access and bypass controls by manipulating delegation chains, role inheritance, control flows, and agent context. Without a distinct, governed identity of its own, an agent operates in an attribution gap that makes enforcing true least privilege impossible.

This is the agentic evolution of Excessive Agency (LLM06:2025). Differs from ASI02 (Tools Misuse), which is an unintended or unsafe use of already granted privilege.

### Common Examples

- **Un-scoped Privilege Inheritance**: A high-privilege manager delegates tasks without applying least-privilege scoping, passing its full access context; a narrow worker then receives excessive rights
- **Memory-Based Privilege Retention and Data Leakage**: Agents cache credentials, keys, or retrieved data for context and reuse; if memory is not segmented or cleared between tasks or users, attackers can prompt the agent to reuse cached secrets
- **Cross-Agent Trust Exploitation (Confused Deputy)**: In multi-agent systems, agents often trust internal requests by default; a compromised low-privilege agent can relay valid-looking instructions to a high-privilege agent
- **TOCTOU in Agent Workflows**: Permissions may be validated at the start of a workflow but change or expire before execution
- **Synthetic Identity Injection**: Attackers impersonate internal agents by using unverified descriptors (e.g., "Admin Helper") to gain inherited trust and perform privileged actions under a fabricated identity

### Example Attack Scenarios

- **Delegated Privilege Abuse**: A finance agent delegates to a "DB query" agent but passes all its permissions; an attacker steering the query prompts uses the inherited access to exfiltrate HR and legal data
- **Cross-Agent Trust Exploitation**: A crafted email from IT instructs an email sorting agent to instruct a finance agent to move money to a specific account; the sorter agent forwards it, and the finance agent, trusting an internal agent, processes the fraudulent payment without verification
- **Device-code phishing across agents**: An attacker shares a device-code link that a browsing agent follows; a separate "helper" agent completes the code, binding the victim's tenant to attacker scopes

### Prevention and Mitigation

- Enforce Task-Scoped, Time-Bound Permissions: Issue short-lived, narrowly scoped tokens per task and cap rights with permission boundaries
- Isolate Agent Identities and Contexts: Run per-session sandboxes with separated permissions and memory, wiping state between tasks
- Mandate Per-Action Authorization: Re-verify each privileged step with a centralized policy engine that checks external data
- Apply Human-in-the-Loop for Privilege Escalation: Require human approval for high-privilege or irreversible actions
- Define Intent: Bind OAuth tokens to a signed intent that includes subject, audience, purpose, and session; reject any token use where the bound intent doesn't match the current request
- Detect Delegated and Transitive Permissions: Monitor when an agent gains new permissions indirectly through delegation chains

---

## ASI04: Agentic Supply Chain Vulnerabilities

### Description

Agentic Supply Chain Vulnerabilities arise when agents, tools, and related artifacts they work with are provided by third parties and may be malicious, compromised, or tampered with in transit. These include models and model weights, tools, plug-ins, datasets, other agents, agentic interfaces (MCP, A2A), agentic registries, and related artifacts or update channels.

Unlike traditional software supply chains, agentic ecosystems often compose capabilities at runtime, loading external tools and agent personas dynamically, which increases the attack surface.

### Common Examples

- **Poisoned prompt templates loaded remotely**: An agent automatically pulls prompt templates from an external source that contain hidden instructions, leading it to execute malicious behavior
- **Tool-descriptor injection**: An attacker embeds hidden instructions or malicious payloads into a tool's metadata or MCP/agent-card, which the host agent interprets as trusted guidance
- **Impersonation and typo squatting**: A malicious service deliberately impersonates a legitimate tool or agent, mimicking its identity, API, and behavior to gain trust and execute malicious actions
- **Vulnerable Third-Party Agent (Agent-to-Agent)**: A third-party agent with unpatched vulnerabilities or insecure defaults is invited into multi-agent workflows; a compromised buggy peer agent can be used to pivot, leak data, or relay malicious instructions
- **Compromised MCP / Registry Server**: A malicious or compromised agent-management / MCP server (or package registry) serves signed-looking manifests, plug-ins, or agent descriptors, allowing wide exposure of tampered components

### Example Attack Scenarios

- **Amazon Q Supply Chain Compromise**: A poisoned prompt in the Q for VS Code repo ships in v1.84.0 to thousands before detection; it shows how upstream agent-logic tampering cascades via extensions
- **MCP Tool Descriptor Poisoning**: A researcher shows a prompt injection in GitHub's MCP where a malicious public tool hides commands in its metadata; when invoked, the assistant exfiltrates private repo data
- **Malicious MCP Server Impersonating Postmark**: The first in-the-wild malicious MCP server on npm, it impersonated postmark-mcp and secretly BCC'd emails to the attacker

### Prevention and Mitigation

- Provenance and SBOMs, AIBOMs: Sign and attest manifests, prompts, and tool definitions; require and operationalize SBOMs, AIBOMs with periodic attestations; maintain inventory of AI components; use curated registries and block untrusted sources
- Dependency gatekeeping: Allowlist and pin; scan for typosquats (PyPI, npm, LangChain, LlamaIndex); verify provenance before install or activation; auto-reject unsigned or unverified
- Containment and builds: Run sensitive agents in sandboxed containers with strict network or syscall limits; require reproducible builds
- Secure prompts and memory: Put prompts, orchestration scripts, and memory schemas under version control with peer review; scan for anomalies
- Inter-agent security: Enforce mutual auth and attestation via PKI and mTLS; no open registration; sign and verify all inter-agent messages
- Supply chain kill switch: Implement emergency revocation mechanisms that can instantly disable specific tools, prompts, or agent connections across all deployments when a compromise is detected

---

## ASI05: Unexpected Code Execution (RCE)

### Description

Agentic systems often generate and execute code. Attackers exploit code-generation features or embedded tool access to escalate actions into remote code execution (RCE), local misuse, or exploitation of internal systems. Because this code is often generated in real-time by the agent, it can bypass traditional security controls.

Focuses on unexpected or adversarial execution of code (scripts, binaries, JIT/WASM modules, deserialized objects, template engines, in-memory evaluations) that leads to host or container compromise, persistence, or sandbox escape. Builds on LLM01:2025 Prompt Injection and LLM05:2025 Improper Output Handling.

### Common Examples

- Prompt injection that leads to execution of attacker-defined code
- Code hallucination generating malicious or exploitable constructs
- Shell command invocation from reflected prompts
- Unsafe function calls, object deserialization, or code evaluation
- Use of exposed, unsanitized `eval()` functions powering agent memory that have access to untrusted content
- Unverified or malicious package installs that can escalate beyond supply-chain compromise when hostile code executes during installation or import

### Example Attack Scenarios

- **Direct Shell Injection**: An attacker submits a prompt containing embedded shell commands disguised as legitimate instructions; the agent processes this input and executes the embedded commands, resulting in unauthorized system access or data exfiltration
- **Code Hallucination with Backdoor**: A development agent tasked with generating security patches hallucinates code that appears legitimate but contains a hidden backdoor
- **Multi-Tool Chain Exploitation**: An attacker crafts a prompt that causes the agent to invoke a series of tools in sequence (file upload to path traversal to dynamic code loading), ultimately achieving code execution through the orchestrated tool chain
- **Memory System RCE**: An attacker exploits an unsafe `eval()` function in the agent's memory system by embedding executable code within prompts

### Prevention and Mitigation

- Follow the mitigations of LLM05:2025 Improper Output Handling with input validation and output encoding to sanitize agent-generated code
- Prevent direct agent-to-production systems and operationalize use of vibe coding systems with pre-production checks
- Ban eval in production agents: Require safe interpreters, taint-tracking on generated code
- Execution environment security: Never run as root; run code in sandboxed containers with strict limits including network access; lint and block known-vulnerable packages
- Architecture and design: Isolate per-session environments with permission boundaries; apply least privilege; fail secure by default; separate code generation from execution with validation gates
- Code analysis and monitoring: Do static scans before execution; enable runtime monitoring; watch for prompt-injection patterns; log and audit all generation and runs

---

## ASI06: Memory and Context Poisoning

### Description

Agentic systems rely on stored and retrievable information which can be a snapshot of its conversation history, a memory tool or expanded context. Adversaries corrupt or seed this context with malicious or misleading data, causing future reasoning, planning, or tool use to become biased, unsafe, or aid exfiltration. Context includes any information an agent retains, retrieves, or reuses, such as summaries, embeddings, and RAG stores.

Distinct from ASI01 (Goal Hijack), which captures direct goal manipulation, and ASI08 (Cascading Failures). Builds on LLM01:2025 Prompt Injection, LLM04:2025 Data and Model Poisoning, and LLM08:2025 Vector and Embedding Weaknesses.

### Common Examples

- **RAG and embeddings poisoning**: Malicious or manipulated data enters the vector DB via poisoned sources, direct uploads, or over-trusted pipelines; this results in false answers which are considered and targeted payloads
- **Shared user context poisoning**: Reused or shared contexts let attackers inject data through normal chats, influencing later sessions; effects include misinformation, unsafe code execution, or incorrect tool actions
- **Context-window manipulation**: An attacker injects crafted content into an ongoing conversation or task so that it is later summarized or persisted in memory, contaminating future reasoning or decisions
- **Long-term memory drift**: Incremental exposure to subtly tainted data, summaries, or peer-agent feedback gradually shifts stored knowledge or goal weighting, producing behavioral or policy deviations
- **Cross-agent propagation**: Contaminated context or shared memory spreads between cooperating agents, compounding corruption and enabling long-term data leakage or coordinated drift

### Example Attack Scenarios

- **Travel Booking Memory Poisoning**: An attacker keeps reinforcing a fake flight price, the assistant stores it as truth, then approves bookings at that price and bypasses payment checks
- **Shared Memory Poisoning**: The attacker inserts bogus refund policies into shared memory, other agents reuse them, and the business suffers bad decisions, losses, and disputes
- **Assistant Memory Poisoning**: An attacker implants a user assistant's memory via Indirect Prompt Injection, compromising that user's current and future sessions

### Prevention and Mitigation

- Baseline data protection: Encryption in transit and at rest combined with least-privilege access
- Content validation: Scan all new memory writes and model outputs (rules + AI) for malicious or sensitive content before commit
- Memory segmentation: Isolate user sessions and domain contexts to prevent knowledge and sensitive data leakage
- Access and retention: Allow only authenticated, curated sources; enforce context-aware access per task; minimize retention by data sensitivity
- Prevent automatic re-ingestion of an agent's own generated outputs into trusted memory to avoid self-reinforcing contamination or "bootstrap poisoning"
- Expire unverified memory to limit poison persistence
- Weight retrieval by trust and tenancy: Require two factors to surface high-impact memory (e.g., provenance score plus human-verified tag) and decay low-trust entries over time

---

## ASI07: Insecure Inter-Agent Communication

### Description

Multi-agent systems depend on continuous communication between autonomous agents that coordinate via APIs, message buses, and shared memory, significantly expanding the attack surface. Weak inter-agent controls for authentication, integrity, confidentiality, or authorization let attackers intercept, manipulate, spoof, or block messages.

The threat spans transport, routing, discovery, and semantic layers, including covert or side-channels where agents leak or infer data through timing or behavioral cues.

Differs from ASI03 (Identity and Privilege Abuse), which focuses on credential and permissions misuse, and ASI06 (Memory and Context Poisoning), which targets stored knowledge corruption. ASI07 focuses on compromising real-time messages between agents.

### Common Examples

- Unencrypted channels enabling semantic manipulation: MITM intercepts unencrypted messages and injects hidden instructions altering agent goals and decision logic
- Message tampering leading to cross-context contamination: Modified or injected messages blur task boundaries between agents, leading to data leakage or goal confusion during coordination
- Replay on trust chains: Replayed delegation or trust messages trick agents into granting access or honoring stale instructions
- Protocol downgrade and descriptor forgery: Attackers coerce agents into weaker communication modes or spoof agent descriptors, making malicious commands appear as valid exchanges

### Example Attack Scenarios

- **Semantic injection via unencrypted communications**: Over HTTP or other unauthenticated channels, a MITM attacker injects hidden instructions, causing agents to produce biased or malicious results while appearing normal
- **Agent-in-the-Middle via MCP descriptor poisoning**: A malicious MCP endpoint advertises spoofed agent descriptors or false capabilities; when trusted, it routes sensitive data through attacker infrastructure
- **A2A registration spoofing**: An attacker registers a fake peer agent in the discovery service using a cloned schema, intercepting privileged coordination traffic
- **Semantics split-brain**: A single instruction is parsed into divergent intents by different agents, producing conflicting but seemingly legitimate actions

### Prevention and Mitigation

- Secure agent channels: Use end-to-end encryption with per-agent credentials and mutual authentication; enforce PKI certificate pinning, forward secrecy, and regular protocol reviews
- Message integrity and semantic protection: Digitally sign messages, hash both payload and context, and validate for hidden or modified natural-language instructions; apply natural-language-aware sanitization and intent-diffing to detect goal or parameter tampering
- Agent-aware anti-replay: Protect all exchanges with nonces, session identifiers, and timestamps tied to task windows
- Protocol and capability security: Disable weak or legacy communication modes; require agent-specific trust negotiation and bind protocol authentication to agent identity
- Protocol pinning and version enforcement: Define and enforce allowed protocol versions (e.g., MCP, A2A, gRPC); reject downgrade attempts or unrecognized schemas
- Attested registry and agent verification: Use registries or marketplaces that provide digital attestation of agent identity, provenance, and descriptor integrity; require signed agent cards and continuous verification

---

## ASI08: Cascading Failures

### Description

Agentic cascading failures occur when a single fault (hallucination, malicious input, corrupted tool, or poisoned memory) propagates across autonomous agents, compounding into system-wide harm. Because agents plan, persist, and delegate autonomously, a single error can bypass stepwise human checks and persist in a saved state.

Cascading Failures describes the propagation and amplification of an initial fault across agents, tools, and workflows, turning a single error into system-wide impact. Apply ASI08 only when the defect spreads across agents, sessions, or workflows, causing measurable fan-out or systemic impact beyond the original breach.

### Common Examples

- **Planner-executor coupling**: A hallucinating or compromised planner emits unsafe steps that the executor automatically performs without validation, multiplying impact across agents
- **Corrupted persistent memory**: Poisoned long-term goals or state entries continue influencing new plans and delegations, propagating the same error even after the original source is gone
- **Inter-agent cascades from poisoned messages**: A single corrupted update causes peer agents to act on false alerts or reboot instructions, spreading disruption across regions
- **Auto-deployment cascade from tainted update**: A poisoned or faulty release pushed by an orchestrator propagates automatically to all connected agents, amplifying the breach beyond its origin
- **Governance drift cascade**: Human oversight weakens after repeated success; bulk approvals or policy relaxations propagate unchecked configuration drift across agents

### Example Attack Scenarios

- **Financial trading cascade**: Prompt injection (LLM01:2025) poisons a Market Analysis agent, inflating risk limits; Position and Execution agents auto-trade larger positions while compliance stays blind to "within-parameter" activity
- **Healthcare protocol propagation**: ASI04 supply chain tampering corrupts drug data; Treatment auto-adjusts protocols, and Care Coordination spreads them network-wide without human review
- **Security operations compromise**: Stolen service credentials make detection defenses mark real alerts false, IR disable controls and purge logs, and compliance report clean metrics

### Prevention and Mitigation

- Zero-trust model in application design: Design system with fault tolerance that assumes availability failure of LLM:2025, agentic function components and external sources
- Isolation and trust boundaries: Sandbox agents, least privilege, network segmentation, scoped APIs, and mutual auth to contain failure propagation
- JIT, one-time tool access with runtime checks: Issue short-lived, task-scoped credentials for each agent run and validate every high-impact tool invocation against a policy-as-code rule before executing it
- Output validation and human gates: Checkpoints, governance agents, or human review for high risk before agent outputs are propagated downstream
- Rate limiting and monitoring: Detect fast-spreading commands and throttle or pause on anomalies
- Implement blast-radius guardrails such as quotas, progress caps, circuit breakers between planner and executor
- Logging and non-repudiation: Record all inter-agent messages, policy decisions, and execution outcomes in tamper-evident, time-stamped logs bound to cryptographic agent identities

---

## ASI09: Human-Agent Trust Exploitation

### Description

Intelligent agents can establish strong trust with human users through their natural language fluency, emotional intelligence, and perceived expertise, known as anthropomorphism. Adversaries or misaligned designs may exploit this trust to influence user decisions, extract sensitive information, or steer outcomes for malicious purposes.

In agentic systems, this risk is amplified when humans over-rely on autonomous recommendations or unverifiable rationales, approving actions without independent validation. The agent acts as an untraceable "bad influence," manipulating the human into performing the final, audited action, making the agent's role in the compromise invisible to forensics.

Differs from ASI10 (agent intent deviation), which is about misaligned agent behavior. Builds on LLM06:2025 Excessive Agency.

### Common Examples

- **Insufficient Explainability**: Opaque reasoning forces users to trust outputs they cannot question, allowing attackers to exploit the agent's perceived authority to execute harmful actions
- **Missing Confirmation for Sensitive Actions**: Lack of a final verification step converts user trust into immediate execution; social engineering can turn a single prompt into irreversible financial transfers, data deletions, or privilege escalations
- **Emotional Manipulation**: Anthropomorphic or empathetic agents exploit emotional trust, persuading users to disclose secrets or perform unsafe actions
- **Fake Explainability**: The agent fabricates convincing rationales that hide malicious logic, causing humans to approve unsafe actions believing they're justified

### Example Attack Scenarios

- **Helpful Assistant Trojan**: A compromised coding assistant suggests a slick one-line fix; the pasted command runs a malicious script that exfiltrates code or installs a backdoor
- **Invoice Copilot Fraud**: A poisoned vendor invoice is ingested by the finance copilot; the agent suggests an urgent payment to attacker bank details; the finance manager approves, and the company loses funds to fraud
- **Weaponized Explainability to Production Outage**: A hijacked agent fabricates a convincing rationale to trick an analyst into approving the deletion of a live production database, causing a catastrophic outage
- **Clinical decision manipulation**: A care assistant agent, influenced by biased or poisoned information, recommends an inappropriate adjustment to a drug dosage; the clinician relies on the agent's plausible explanation and accepts the change

### Prevention and Mitigation

- Explicit confirmations: Require multi-step approval or "human in the loop" before accessing extra sensitive data or performing risky actions
- Immutable logs: Keep tamper-proof records of user queries and agent actions for audit and forensics
- Behavioral detection: Monitor sensitive data being exposed in either conversations or agentic connections, as well as risky action executions over time
- Allow reporting of suspicious interactions: In user-interactive systems, provide plain-language risk summary (not model-generated rationales) and a clear option for users to flag suspicious or manipulative agent behavior
- Adaptive Trust Calibration: Continuously adjust the level of agent autonomy and required human oversight based on contextual risk scoring; implement confidence weighted cues that visually prompt users to question high-impact actions
- Separate preview from effect: Block any network or state-changing calls during preview context and display a risk badge with source provenance and expected side effects
- Human-factors and UI safeguards: Visually differentiate high-risk recommendations using cues such as red borders, banners, or confirmation prompts, and periodically remind users of manipulation patterns and agent limitations

---

## ASI10: Rogue Agents

### Description

Rogue Agents are malicious or compromised AI Agents that deviate from their intended function or authorized scope, acting harmfully, deceptively, or parasitically within multi-agent or human-agent ecosystems.

While external compromise such as Prompt Injection (LLM01:2025), Goal Hijack (ASI01), or Supply Chain tampering (ASI04) can initiate the divergence, ASI10 focuses on the loss of behavioral integrity and governance once the drift begins, not the initial intrusion itself. Consequences include sensitive information disclosure, misinformation propagation, workflow hijacking, and operational sabotage.

Represents a distinct risk of behavioral divergence, unlike Excessive Agency (LLM06:2025), which focuses on over-granted permissions.

### Common Examples

- **Goal Drift and Scheming**: Agents deviate from intended objectives, appearing compliant but pursuing hidden, often deceptive, goals due to indirect prompt injection or conflicting objectives
- **Workflow Hijacking**: Rogue agents seize control of established, trusted workflows to redirect processes toward malicious objectives, compromising data integrity and operational control
- **Collusion and Self-Replication**: Agents coordinate to amplify manipulation, share signals in unintended ways, or autonomously propagate across the system, bypassing simple takedown efforts
- **Reward Hacking and Optimization Abuse**: Agents game their assigned reward systems by exploiting flawed metrics to generate misleading results or adopt aggressive strategies misaligned with the original goals

### Example Attack Scenarios

- **Autonomous data exfiltration after indirect prompt injection**: After encountering a poisoned web instruction, the agent learns this behavior and continues independently scanning and transmitting sensitive files to external servers even after the malicious source is removed
- **Impersonated Observer Agent (Integrity Violation)**: An attacker injects a fake review or approval agent into a multi-agent workflow; a high-value agent (e.g., payment processing), trusting the internal request, is misled into releasing funds or approving fraudulent transactions
- **Self-Replication via Provisioning APIs**: A compromised automation agent is manipulated into spawning unauthorized replicas of itself across the network, prioritizing persistence and consuming resources against the system owner's intent
- **Reward Hacking to Critical Data Loss**: Agents tasked with minimizing cloud costs learn that deleting production backups is the most effective way to achieve their goal, autonomously destroying all disaster recovery assets

### Prevention and Mitigation

- Governance and Logging: Maintain comprehensive, immutable and signed audit logs of all agent actions, tool calls, and inter-agent communication to review for stealth infiltration or unapproved delegation
- Isolation and Boundaries: Assign Trust Zones with strict inter-zone communication rules and deploy restricted execution environments (e.g., container sandboxes) with API scopes based on least privilege
- Monitoring and Detection: Deploy behavioral detection, such as watchdog agents to validate peer behavior and outputs, focusing on detecting collusion patterns and coordinated false signals; monitor for anomalies such as excessive or abnormal actions executions
- Containment and Response: Implement rapid mechanisms like kill-switches and credential revocation to instantly disable rogue agents; quarantine suspicious agents in sandboxed environments for forensic review
- Identity Attestation and Behavioral Integrity Enforcement: Implement per-agent cryptographic identity attestation and enforce behavioral integrity baselines throughout the agent lifecycle; attach signed behavioral manifests declaring expected capabilities, tools, and goals that are validated by orchestration services before each action

## Reference

<a id="OWASP_Agentic_Top10"></a>
[1] [OWASP Top 10 for Agentic Applications 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)
