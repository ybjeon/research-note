# Security Implementation in Enterprise Agent Platform

## 1. Goal

Compare enterprise generative AI platforms from a **security implementation perspective**.

The core question is:

> In this platform, under whose authority does the Agent operate, which Tools or MCPs does it invoke, and how are those actions controlled and audited?

### Main Points

* User / Agent Identity
* Permission Delegation
* Least Privilege per Agent
* Tool / MCP Control
* Guardrails
* Content Filtering
* Prompt Injection Defense
* DLP / Sensitive Data Detection
* Network Isolation
* Audit / Logging
* Observability
* Data Retention / Residency
* SIEM / SOC Integration

---

## 2. Platforms Under Comparison

| Vendor    | Enterprise AI Assistant | Agent / AI Platform                                        |
| --------- | ----------------------- | ---------------------------------------------------------- |
| OpenAI    | ChatGPT Enterprise      | OpenAI API, Responses API, Agents SDK                      |
| Anthropic | Claude Enterprise       | Claude API, MCP                                            |
| Google    | Gemini Enterprise       | Vertex AI, Gemini Enterprise Agent Platform, Agent Builder |
| AWS       | Amazon Q Business       | Amazon Bedrock, Bedrock Agents, AgentCore                  |
| Microsoft | Microsoft 365 Copilot   | Azure AI Foundry, Copilot Studio                           |

---

## 3. Key Security Comparison Items

| Security Item                  | Core Question                                                                                       |
| ------------------------------ | --------------------------------------------------------------------------------------------------- |
| Identity                       | Can User and Agent each be individually identified?                                                 |
| Permission Delegation          | Does the Agent act under user permissions or its own permissions?                                   |
| Agent Permission Isolation     | Can least-privilege be granted per Agent?                                                           |
| Tool Control                   | Can the APIs, SaaS, DBs, and business systems an Agent can invoke be restricted?                    |
| MCP Control                    | Can external MCP Servers be allowed or blocked?                                                     |
| MCP Allowlist                  | Can only approved MCP Servers be connected?                                                         |
| Guardrails                     | Can input, output, Tool invocations, and Memory writes be restricted by policy?                     |
| Content Filtering              | Can harmful content, policy violations, and inappropriate responses be detected and blocked?        |
| Prompt Injection Defense       | Can direct/indirect prompt injection be detected and blocked?                                       |
| DLP / Sensitive Data Detection | Can PII, financial data, source code, and trade secrets be detected, masked, or blocked?            |
| Network Control                | Can isolation be achieved via VPC, VNet, Private Endpoint, PrivateLink, etc.?                       |
| Data Access Control            | Are original system permissions inherited, or is it controlled via separate IAM?                    |
| Audit / Logging                | Can prompts, Tool calls, Agent executions, and admin changes be audited?                            |
| Observability                  | Can Agent decision flows, Tool calls, errors, and latency be traced?                                |
| Data Retention                 | Can retention periods for conversations, logs, files, and memory be controlled?                     |
| Data Residency                 | Can the region for data storage and processing be controlled?                                       |
| SIEM / DLP Integration         | Can logs be forwarded to Splunk, Sentinel, Chronicle, etc.?                                         |
| Runtime Security               | Can code execution, browser, shell, and file access be isolated and controlled?                     |
| Evaluation / Red Teaming       | Can Agent security testing and policy evaluation be performed before deployment?                    |

---

## 4. Platform Security Feature Comparison

| Item                         | ChatGPT Enterprise                                              | Claude Enterprise                                                              | Gemini Enterprise / Google Agent Platform             | AWS Bedrock + AgentCore                                  | Microsoft 365 Copilot                              | Azure AI Foundry                                              |
| ---------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------------ | ----------------------------------------------------- | -------------------------------------------------------- | -------------------------------------------------- | ------------------------------------------------------------- |
| Product Type                 | Enterprise AI Assistant                                         | Enterprise AI Assistant                                                        | Assistant + Agent Platform                            | Agent Platform                                           | Enterprise AI Assistant                            | Agent Platform                                                |
| Primary Users                | Employees                                                       | Employees                                                                      | Employees + Developers                                | Developers / Platform Teams                              | Employees                                          | Developers / Platform Teams                                   |
| Default Identity             | Workspace User                                                  | Claude Organization User                                                       | Google Cloud / Workspace User                         | IAM principal / Agent identity                           | Entra ID User                                      | Entra ID User / Agent identity                                |
| Agent Identity               | Limited, user/workspace-centric                                 | Limited, user/organization-centric                                             | Agent Identity supported                              | AgentCore Identity                                       | Copilot Agents based on M365/Entra permission model | Microsoft Entra-based Agent Identity supported                |
| Permission Delegation Model  | User permissions + connector permissions-centric                | User/organization permissions-centric                                          | Agent can act as itself or on behalf of end user      | Agent own permissions and IAM-based delegation designable | User Microsoft Graph permissions inheritance-centric | Agent identity + delegated access designable                  |
| User Permission Inheritance  | Per-connector permission inheritance                            | Per-connector permission inheritance                                           | Workspace / Cloud IAM / Data source ACL               | Requires direct design                                   | SharePoint, Exchange, Teams, Graph permission inheritance | Data source / Azure RBAC / Entra-based design            |
| Agent Permission Isolation   | Limited                                                         | Limited                                                                        | Available                                             | Strong                                                   | Configurable per Copilot Studio / Agent            | Strong                                                        |
| External MCP Allowance       | Conditionally allowed based on Connector / Actions / MCP ecosystem | Integrated with Claude Desktop / Connectors / MCP ecosystem                 | Controlled via Google Agent Platform / Gateway family | AgentCore Gateway provides MCP-compatible tool interface | Copilot Studio / Graph connector-centric           | MCP tools routable through AI Gateway for control             |
| MCP Allowlist                | Depends on product/admin policy                                 | Depends on product/admin policy                                                | Gateway / IAM / policy-based design                   | Gateway / IAM / Policy-based design                      | Tenant / connector policy-centric                  | AI Gateway applies auth, rate limit, IP restriction, audit logging |
| Tool Permission Control      | Workspace Admin / Connector settings                            | Organization Admin / Connector settings                                        | IAM, Agent Identity, Connector settings               | IAM, AgentCore Policy, Gateway                           | Entra, Purview, Copilot Studio, Graph permissions  | Entra ID, Azure RBAC, AI Gateway, API Management              |
| Network Isolation            | SaaS-based, direct customer VPC control limited                 | SaaS-based, network control limited                                            | Google Cloud-based control available                  | VPC, PrivateLink, IAM endpoint designable                | SaaS-based M365 boundary                           | VNet, Private Endpoint, Azure Policy                          |
| Audit Logs                   | Compliance Platform / Admin & Audit Logs API                    | Enterprise audit logs, Compliance API                                          | Cloud Audit Logs, usage audit logs, traces/spans      | CloudTrail, CloudWatch, AgentCore Observability          | Microsoft Purview Audit                            | Azure Monitor, Log Analytics, Purview                         |
| Tool Call Audit              | Verify Compliance log coverage                                  | Verify Compliance API / audit log coverage                                     | Agent Platform audit logging                          | AgentCore Observability / Gateway / CloudTrail           | Purview Audit / Copilot audit logs                 | AI Gateway audit logging / Azure Monitor                      |
| Prompt / Response Logging    | eDiscovery, DLP, SIEM integration available via Compliance Platform | activity logs, chats, files, projects, users accessible via Compliance API | Usage audit logs / Cloud Logging / trace              | Application design + CloudWatch / CloudTrail             | Verify Purview Audit / eDiscovery / DSPM for AI    | Foundry tracing / Azure Monitor design                        |
| Admin Change Audit           | Supported                                                       | Supported                                                                      | Cloud Audit Logs                                      | CloudTrail                                               | Purview Audit                                      | Azure Activity Log / Monitor                                  |
| Data Retention               | Enterprise retention policy                                     | Custom data retention controls                                                 | Workspace / Cloud retention policy                    | Customer account log/storage policy                      | M365 / Purview retention                           | Azure / Purview retention                                     |
| Guardrails                   | GPT settings, Admin policy, Connector/Action control-centric    | Claude safety policy, organization policy-centric                              | Gemini safety / Vertex AI safety / policy             | Bedrock Guardrails + AgentCore Policy                    | Purview, DLP, sensitivity labels, Copilot policy   | Azure AI Content Safety, Prompt Shields, policy               |
| Content Filtering            | OpenAI model safety and workspace policy-based                  | Claude safety policy-based                                                     | Gemini safety filters                                 | Bedrock Guardrails content filters                       | Microsoft safety stack + Purview policy            | Azure AI Content Safety                                       |
| Prompt Injection Defense     | Depends on Connector/Action design and app policy               | Depends on Claude safety and client/tool policy                                | Workspace Gemini emphasizes layered defense           | Bedrock Guardrails prompt attack filter + Gateway design | M365 security model + Purview + connector policy   | Prompt Shields detects direct/indirect prompt injection       |
| DLP Integration              | Compliance Platform integrated with DLP/eDiscovery/SIEM         | Compliance API integrable with external SIEM/DLP                               | Google DLP / Workspace DLP / Cloud Logging linkage    | Macie, DLP pipeline, CloudWatch/SIEM design required     | Microsoft Purview DLP strength                     | Microsoft Purview, Defender, Sentinel integration             |
| Data Residency               | Verify Enterprise contract/region options                       | Verify Enterprise contract/region options                                      | Verify Google Cloud/Workspace region policy           | Designable by region, VPC, account                       | M365 data residency policy                         | Azure region / data boundary / private deployment             |
| Encryption                   | Encryption at rest/in transit                                   | Encryption at rest/in transit                                                  | Google Cloud encryption                               | KMS / CMK designable                                     | M365 encryption                                    | Key Vault / CMK available                                     |
| Customer Managed Key         | Verify Enterprise Key Management scope                          | Verify available scope                                                         | Verify Google Cloud CMEK available scope              | KMS-based, strong                                        | Verify Microsoft 365 Customer Key available scope  | Azure Key Vault / CMK, strong                                 |
| Model/Data Isolation         | Enterprise data training opt-out policy                         | Enterprise data control policy                                                 | Workspace/Cloud customer data protection policy       | Bedrock does not train on customer input/output          | M365 tenant boundary                               | Azure tenant/subscription/resource boundary                   |
| Abuse Monitoring             | Admin/Compliance log-based detection                            | Audit/Compliance API-based detection                                           | Cloud Logging / Audit Logs-based detection            | CloudTrail, CloudWatch, GuardDuty integration            | Purview Audit / Defender / Sentinel                | Azure Monitor / Defender / Sentinel                           |
| Rate Limiting                | SaaS policy and API limits                                      | SaaS/API limits                                                                | Google Cloud quota/policy                             | API Gateway, WAF, Lambda, service quota                  | M365 service limits / tenant policy                | API Management, Gateway, Azure policy                         |
| Egress Control               | Direct network egress control limited (SaaS-based)              | Direct network egress control limited (SaaS-based)                             | VPC Service Controls available in Google Cloud environment | VPC, PrivateLink, NAT, Security Group, Network Firewall | SaaS-based M365 boundary                          | VNet, Private Endpoint, NSG, Firewall                         |
| Sandbox / Code Execution     | Review Advanced Data Analysis / Actions usage policy            | Review Claude Code / Desktop / Tool usage policy                               | Separate isolation required when using code execution features | AgentCore Code Interpreter, Lambda, container sandbox design | Copilot Studio agent action control            | Code Interpreter / tool execution environment control         |
| File Upload Control          | Workspace/Admin policy                                          | Organization policy                                                            | Workspace/Cloud policy                                | S3/IAM/KMS/AV scan design                                | Purview, sensitivity labels                        | Storage, Defender, Purview policy                             |
| Connector Governance         | Workspace Admin, Connector policy                               | Connector policy                                                               | Workspace / Agent Builder connector policy            | AgentCore Gateway / IAM                                  | Graph connectors, Copilot Studio, Purview          | API Management, AI Gateway, Connector policy                  |
| Tool Input/Output Filtering  | Supplemented with app/Connector design and Compliance logs      | Supplemented with app/MCP Gateway design                                       | Cloud Logging, policy, safety filter combination      | Gateway + Guardrails + Lambda validation                 | Purview/DLP/Connector policy                       | AI Gateway + Content Safety                                   |
| Human Approval               | Implemented per GPT/Action design                               | Implemented per Tool/MCP design                                                | Implemented in Agent workflow                         | Step Functions, Lambda, approval workflow design         | Power Automate / Copilot Studio approval           | Logic Apps / Power Automate / workflow approval               |
| Evaluation / Red Teaming     | Separate evaluation framework required before Enterprise deployment | Separate evaluation framework required                                     | Vertex AI evaluation family available                 | Bedrock evaluation / custom red team design              | Microsoft Responsible AI / Purview review          | Azure AI evaluation, red teaming, Content Safety              |
| Groundedness Detection       | Supplemented with app/RAG design                                | Supplemented with app/RAG design                                               | Grounding/search quality management required          | Knowledge Bases + evaluation design                      | Graph grounding + Purview                          | Azure AI Content Safety groundedness detection                |
| Protected Material Detection | Policy/model safety-based                                       | Policy/model safety-based                                                      | Policy/model safety-based                             | Guardrails/policy design                                 | Microsoft policy-based                             | Azure protected material detection                            |
| SIEM Integration             | Compliance Platform → SIEM                                      | Compliance API → SIEM/Datadog etc.                                             | Cloud Logging export                                  | CloudWatch/CloudTrail → SIEM                             | Sentinel / Purview / Defender                      | Sentinel / Monitor / Log Analytics                            |
| Incident Response            | Reconstruction via Compliance logs                              | Reconstruction via Compliance API                                              | Based on Cloud Audit Logs / traces                    | Detailed reconstruction with AgentCore Observability + CloudTrail | Purview Audit / Defender                    | Azure Monitor / Sentinel                                      |
| Security Implementation Flexibility | Medium                                                    | Medium                                                                         | Medium–High                                           | Very High                                                | Medium                                             | High                                                          |
| Security Operation Complexity | Low–Medium                                                     | Low–Medium                                                                     | Medium                                                | High                                                     | Medium                                             | Medium–High                                                   |

---

## 5. Core Comparison: User Permissions vs Agent Permissions

The most important question in AI Agent security is:

> Under whose authority does the Agent operate?

There are two main models.

---

## 5.1 User-delegated Model

The user-delegated model is an approach where the Agent acts by receiving delegated user permissions.

An example is as follows.

```text
User: Youngbae
  ↓
Agent
  ↓
SharePoint / Google Drive / Jira
```

In this case, the Agent should only be able to see documents that Youngbae can access.

### Products Close to This Model

| Product               | Description                                                    |
| --------------------- | -------------------------------------------------------------- |
| Microsoft 365 Copilot | Microsoft Graph / SharePoint / Exchange permission inheritance |
| Gemini Enterprise     | Workspace / data source permission inheritance                 |
| ChatGPT Enterprise    | Per-connector user permission-based access                     |
| Claude Enterprise     | Per-connector user/organization permission-based access        |

### Advantages

* Can leverage permissions users already have.
* Well-suited for enterprise-wide Assistants.
* Easier to reduce the risk of data over-exposure.
* Can reuse existing business SaaS permission structure.

### Disadvantages

* Has limitations for Agents to perform complex tasks like independent service accounts.
* Permission debugging can be difficult.
* Tool call scope is tied to SaaS policy.
* If user permissions are excessively broad, AI can also access excessive data.

---

## 5.2 Agent Identity Model

The Agent Identity Model is an approach that assigns a separate identity to the Agent itself.

An example is as follows.

```text
Finance-Agent
  ↓
IAM Role / Managed Identity
  ↓
ERP Read API
  ↓
Return Results
```

In this case, which APIs the Agent can invoke is designed separately, independent of any person's permissions.

### Products Close to This Model

| Product                           | Description                                                            |
| --------------------------------- | ---------------------------------------------------------------------- |
| AWS Bedrock + AgentCore           | AgentCore Identity, IAM, Gateway, Policy-centric                       |
| Azure AI Foundry                  | Microsoft Entra-based Agent Identity, Azure RBAC-centric               |
| Google Agent Platform / Vertex AI | Agent Identity, IAM, VPC Service Controls-based design                 |
| OpenAI API / Agents SDK           | Separate permission model design required at application level         |
| Claude API / MCP                  | Separate permission model design required at application and MCP Gateway level |

### Advantages

* Least privilege design per Agent is possible.
* Tool/API permissions can be finely separated.
* Well-suited for production business automation.
* Easier to clearly define audit trails and accountability.
* Network, data, and API access policies can be separated per Agent.

### Disadvantages

* High design complexity.
* IAM, network, logging, and policy design are required.
* Security team and platform team involvement is essential.
* If designed incorrectly, an Agent may have excessive permissions.

---

## 6. External MCP / Tool Permission Perspective

When allowing external MCPs, the following should be evaluated beyond simply "can it connect":

| Question                                                                          | Importance  |
| --------------------------------------------------------------------------------- | ----------- |
| Can MCP servers be allowlisted?                                                   | Very High   |
| Can tool permissions be restricted per MCP server?                                | Very High   |
| Can a user approval step be inserted before Tool calls?                           | High        |
| Are Tool call logs retained?                                                      | Very High   |
| Is Tool input/output subject to DLP?                                              | Very High   |
| Is MCP server authentication via OAuth, API key, or service account?             | Very High   |
| Can the Agent access the filesystem, shell, or network through MCP servers?      | Very High   |
| Can external SaaS MCPs and internal MCPs be separated?                            | High        |
| Can an MCP Gateway be established?                                                | Very High   |
| Where are secrets stored and how are they rotated?                                | Very High   |

---

## 7. Recommended MCP Security Architecture

It is safer to place a Gateway in the middle rather than connecting MCP directly to the Agent.

The recommended architecture is as follows.

```text
User
  ↓
Enterprise AI / Agent Platform
  ↓
MCP Gateway
  ↓
Approved MCP Servers
  ↓
Internal APIs / SaaS / DB
```

### Items to Control in MCP Gateway

| Control Item      | Description                                              |
| ----------------- | -------------------------------------------------------- |
| Authentication    | MCP client / server authentication                       |
| Authorization     | Tool invocation permissions per Agent                    |
| Allowlist         | Allow only approved MCP servers                          |
| Rate Limit        | Prevent excessive calls                                  |
| IP Restriction    | Restrict to allowed networks                             |
| DLP               | Sensitive data exfiltration detection                    |
| Schema Validation | Tool input/output validation                             |
| Audit Logging     | Record who called which tool                             |
| Human Approval    | Approval required for high-risk actions like write/delete/send |
| Secret Management | Protect API keys, tokens, credentials                    |
| Egress Control    | Restrict traffic going to external networks              |

---

## 8. Detailed Guardrails / Filtering Classification

The term "Guardrails" has slightly different meanings across platforms.

Therefore, checking only "whether Guardrails are supported" is insufficient — it should be broken down as follows:

| Guardrail Type        | Description                                              | Example                                                     |
| --------------------- | -------------------------------------------------------- | ----------------------------------------------------------- |
| Input Guardrail       | Inspects user prompts before model invocation            | Block prohibited requests, jailbreaks, prompt injections    |
| Output Guardrail      | Inspects model responses before showing to users         | Block sensitive data, harmful content, policy-violating responses |
| Tool Input Guardrail  | Inspects Tool call arguments before Agent invokes a Tool | Block calls to `delete_user`, `send_email`, `transfer_money` |
| Tool Output Guardrail | Inspects Tool results before feeding back to model       | Remove hidden prompt injections in documents                |
| Context Guardrail     | Inspects document chunks entering RAG                    | Filter sensitive documents, contaminated documents, malicious instructions |
| Memory Guardrail      | Inspects content before storing in long-term memory      | Block storing SSNs, passwords, access tokens                |
| Action Guardrail      | Policy check before executing actual business actions    | Payments, deletions, permission changes require approval    |
| Policy Guardrail      | Allow/block based on organizational policy               | "No external email sending", "No customer data external transfer" |
| Network Guardrail     | Restrict external call destinations                      | Call only approved APIs/MCP Servers                         |
| Cost Guardrail        | Limit usage and cost                                     | Prevent Agent loops, excessive token/API calls              |

---

## 9. Required Defenses by AI Security Threat

| Threat                      | Description                                              | Required Security Feature                                                       |
| --------------------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------- |
| Prompt Injection            | Attack that causes the model to ignore system instructions | Prompt Shields, input filtering, instruction hierarchy, policy enforcement    |
| Indirect Prompt Injection   | Attack via hidden instructions in documents/webpages/emails | Tool output filtering, document scanning, context isolation                  |
| Jailbreak                   | Attempt to bypass safety policies                        | Jailbreak detection, content filtering, output guardrail                        |
| Data Exfiltration           | Agent transmitting sensitive data externally             | DLP, egress control, tool allowlist, audit logs                                 |
| Tool Abuse                  | Agent calling unauthorized APIs                          | Tool permission, MCP Gateway, IAM, approval workflow                            |
| Over-permissioned Agent     | Agent possessing excessive permissions                   | Least privilege, agent identity, permission boundary                            |
| Credential Leakage          | Secrets exposed in prompts, logs, or memory              | Secret scanning, memory filtering, log redaction                                |
| Unsafe Code Execution       | Agent executing dangerous code                           | Sandboxing, network isolation, file system restriction                          |
| Hallucinated Action         | Business action executed based on incorrect reasoning    | Groundedness detection, human approval, evaluation                              |
| Data Poisoning              | RAG documents or tool output contaminated                | Source validation, content scanning, trusted data pipeline                      |
| Tool Poisoning              | MCP tool descriptions or schema contain malicious instructions | MCP server validation, tool metadata scanning, allowlist                    |
| Agent Loop / Cost Explosion | Agent generating costs through repeated calls            | Rate limit, max step limit, budget guardrail                                    |
| Compliance Violation        | Violation of log/data retention policies                 | Retention policy, audit log, eDiscovery                                         |
| Unauthorized Admin Change   | Admin making dangerous configuration changes             | Admin audit log, change approval, SIEM alert                                    |

---

## 10. Security Implementation Direction by Platform

## 10.1 ChatGPT Enterprise

ChatGPT Enterprise is strong as an enterprise-wide AI Assistant, but is not structured for customers to directly operate an Agent Runtime.

### Security Implementation Points

* SSO / SCIM / RBAC configuration
* Workspace user and group policy configuration
* Per-connector access permission verification
* Compliance Platform integration
* Admin & Audit Logs API integration
* DLP / eDiscovery / SIEM integration
* Policy establishment for allowing external GPTs / Actions / Connectors
* Policy establishment for restricting sensitive data uploads
* Data retention and deletion policy review
* Verify Enterprise Key Management available scope
* Verify zero data retention applicability for API usage

### Questions to Verify

| Question                                                                              | Reason                        |
| ------------------------------------------------------------------------------------- | ----------------------------- |
| Who can view files uploaded by users and conversation logs?                           | Data access control           |
| Does the Connector properly inherit original permissions?                             | Prevent over-exposure         |
| Can admin configuration changes be audited?                                           | Operational audit             |
| Can Compliance logs be forwarded to SIEM?                                             | Security monitoring           |
| Will external GPTs / Actions / Connectors be allowed?                                | External Tool risk management |
| Can Prompt and Response be subject to DLP?                                            | Prevent sensitive data leakage |
| Can data retention period and encryption key control be aligned with organizational policy? | Regulatory compliance    |

---

## 10.2 Claude Enterprise

Claude Enterprise can be viewed as an Enterprise Assistant focused on document analysis, long context, and safety.

### Security Implementation Points

* SSO / SCIM
* Role and permissions
* Audit logs
* Compliance API
* Custom data retention controls
* IP allowlisting / tenant restrictions
* Connector allow policy
* Claude Code / Desktop Extension usage control
* File upload policy
* External MCP usage policy
* API data retention policy review

### Questions to Verify

| Question                                                                              | Reason                       |
| ------------------------------------------------------------------------------------- | ---------------------------- |
| What is the scope of Claude Code's access to local files, repos, and shells?         | Development environment security |
| Will Desktop Extension / Connector usage be allowed?                                 | External extension control   |
| Does the Enterprise audit log and Compliance API provide sufficient traceability?    | Audit trail                  |
| Can data retention period be reduced to align with organizational policy?            | Data governance              |
| Can external MCP server usage be centrally restricted?                               | Tool security                |
| How is zero data retention or retention policy applied when using Claude API?        | Regulatory compliance        |

---

## 10.3 Gemini Enterprise / Google Agent Platform

Gemini mixes both Assistant and Agent Platform characteristics.

### Security Implementation Points

* Google Cloud IAM
* Workspace permission inheritance
* Workforce Identity Federation
* Agent Identity
* Principal Access Boundary
* VPC Service Controls
* Cloud Audit Logs
* Usage audit logs
* Connector error logs
* Trace / span / metrics
* Data source access control
* Vertex AI safety / policy settings
* Prompt injection mitigation review
* Google Cloud DLP / Workspace DLP integration

### Questions to Verify

| Question                                                                              | Reason                        |
| ------------------------------------------------------------------------------------- | ----------------------------- |
| Does the Agent act as itself or on behalf of the end user?                           | Permission delegation model decision |
| Does Google Drive / Workspace permission inheritance work correctly?                 | Prevent document over-exposure |
| Can IAM boundaries be set per Agent Identity?                                        | Agent least privilege         |
| Can Agent actions be viewed separately in Cloud Audit Logs?                          | Audit trail                   |
| Can a data exfiltration boundary be established with VPC Service Controls?           | Network and data boundary     |
| How is indirect prompt injection from external documents or search results mitigated? | Agent safety                 |

---

## 10.4 AWS Bedrock + AgentCore

AWS is closest to a model where customers directly design the security architecture.

### Security Implementation Points

* IAM role per agent
* AgentCore Identity
* AgentCore Gateway
* AgentCore Policy
* AgentCore Observability
* Bedrock Guardrails
* Prompt attack filter
* Content filters
* Sensitive information filter
* VPC / PrivateLink
* KMS encryption
* CloudTrail / CloudWatch
* Secrets Manager
* API Gateway / Lambda permission isolation
* Human approval workflow
* Agent memory security
* Agent execution tracing via CloudWatch traces / spans

### Questions to Verify

| Question                                                                                      | Reason               |
| --------------------------------------------------------------------------------------------- | -------------------- |
| How will IAM Roles be divided per Agent?                                                      | Least privilege design |
| Is user-delegated access needed, or is an Agent service role sufficient?                     | Permission delegation model |
| Should MCP tools be placed behind a Gateway?                                                  | Tool control         |
| Should write actions require human approval?                                                  | High-risk action approval |
| Can the Agent → Tool → Data access flow be reconstructed via CloudTrail?                     | Incident investigation |
| Is information stored in Memory controlled by KMS and retention policy?                      | Memory security      |
| Can outbound network be restricted per Agent?                                                 | Data exfiltration prevention |
| How will Bedrock Guardrails be applied not only to model calls but also at Agent workflow boundaries? | Guardrail coverage |

---

## 10.5 Microsoft 365 Copilot

Microsoft 365 Copilot is most closely aligned with the Microsoft 365 permission model from an Enterprise Assistant security perspective.

### Security Implementation Points

* Entra ID
* Conditional Access
* Microsoft Graph permission
* SharePoint / Exchange / Teams permission cleanup
* Purview Audit
* Purview DLP
* Sensitivity labels
* Data lifecycle management
* Oversharing detection
* Copilot interaction audit
* Copilot Studio agent policy
* External connector policy
* eDiscovery / legal hold
* DSPM for AI review

### Questions to Verify

| Question                                                                                      | Reason                                    |
| --------------------------------------------------------------------------------------------- | ----------------------------------------- |
| Are SharePoint permissions not excessively open?                                              | Prevent Copilot from surfacing excessive documents |
| Is Purview policy applied to prevent Copilot from using sensitive documents in answers?      | Data protection                           |
| Can Prompt / response / Copilot interaction be verified in audit logs or eDiscovery?         | Audit and incident investigation          |
| Do agentic features like Teams Channel Agent not bypass existing security policies?          | Agent security                            |
| Does the Graph connector accurately reflect original permissions?                            | External data permission inheritance      |
| Do sensitivity labels and DLP policies also apply to Copilot responses?                     | Information protection policy consistency |

---

## 10.6 Azure AI Foundry

Azure AI Foundry is the Microsoft-side Agent Platform most similar to AWS AgentCore.

### Security Implementation Points

* Microsoft Entra Agent Identity
* Entra ID
* Azure RBAC
* Project managed identity
* AI Gateway for MCP tools
* Azure API Management
* Private Endpoint / VNet
* Azure Monitor / Log Analytics
* Microsoft Purview
* Azure AI Content Safety
* Prompt Shields
* Groundedness detection
* Protected material detection
* Key Vault
* Policy / blueprint-based control

### Questions to Verify

| Question                                                                                          | Reason                      |
| ------------------------------------------------------------------------------------------------- | --------------------------- |
| Will a separate identity be assigned per Agent?                                                   | Least privilege per Agent   |
| Is a delegated model where the Agent acts on behalf of users required?                           | Permission delegation approach |
| Should external MCP tools only be called through the AI Gateway?                                 | Tool security               |
| Does the Gateway support authentication, rate limit, IP restriction, and audit logging?          | MCP control                 |
| Can Agent execution and data access be linked via Azure Monitor and Purview?                     | Audit and governance        |
| Can operation be achieved without external exposure via Private Endpoint?                        | Network isolation           |
| Can Prompt Shields be applied to both direct and indirect prompt attacks?                        | Prompt injection defense    |

---

## 11. Security Architecture Patterns

## 11.1 Enterprise Assistant Security Pattern

```text
User
  ↓
Enterprise AI Assistant
  ↓
Connector
  ↓
SaaS ACL / Document Permission
  ↓
Allowed Data Only
```

The core principle is that AI follows the user's original permissions as-is.

### Key Controls

* SSO
* SCIM
* RBAC
* Connector permission inheritance
* DLP
* Audit log
* Admin policy
* Data retention
* eDiscovery
* SIEM export
* Prompt / response governance
* File upload control

---

## 11.2 Agent Platform Security Pattern

```text
User
  ↓
Agent Runtime
  ↓
Agent Identity
  ↓
Policy / Gateway
  ↓
Approved Tools
  ↓
Internal APIs / DB / SaaS
```

The core principle is controlling the Agent's own permissions and Tool invocations.

### Key Controls

* Identity per Agent
* IAM role / managed identity per Agent
* Tool allowlist
* MCP Gateway
* API Gateway
* Secret Manager / Key Vault
* VPC / VNet / Private Endpoint
* Guardrails
* Human approval
* Observability
* Audit trail
* Prompt injection defense
* Tool input/output filtering
* Memory filtering
* Runtime sandboxing
* Egress control

---

## 12. Security Evaluation Framework

The following scorecard can be used when comparing each platform:

| Evaluation Area          | Sub-items                                                               | Score |
| ------------------------ | ----------------------------------------------------------------------- | ----- |
| Identity & Access        | User identity, Agent identity, delegated access, least privilege        | 1–5   |
| Tool & MCP Security      | Tool allowlist, MCP Gateway, tool permission, tool audit                | 1–5   |
| Guardrails               | Input/output/tool/context/memory/action guardrails                      | 1–5   |
| Content Safety           | Harmful content, jailbreak, protected material, groundedness            | 1–5   |
| Prompt Injection Defense | Direct/indirect prompt injection detection and blocking                 | 1–5   |
| Data Protection          | DLP, PII detection, masking, redaction, file control                    | 1–5   |
| Runtime Security         | Sandbox, code execution control, browser isolation, egress control      | 1–5   |
| Network Security         | VPC/VNet, Private Endpoint, PrivateLink, firewall, egress allowlist     | 1–5   |
| Cryptography             | Encryption at rest/in transit, CMK, key rotation                        | 1–5   |
| Logging & Audit          | Prompt, response, tool call, admin change, identity event logs          | 1–5   |
| Observability            | Trace, span, execution graph, latency, error, step-level debug          | 1–5   |
| Governance               | Retention, residency, legal hold, eDiscovery, classification            | 1–5   |
| SIEM/SOC Integration     | Sentinel, Splunk, Chronicle, Datadog, Cloud SIEM integration            | 1–5   |
| Evaluation & Red Teaming | Security testing, policy eval, regression test, release gate            | 1–5   |
| Incident Response        | Agent execution reconstruction, containment, forensics, alerting        | 1–5   |
| Secret Management        | Tool credential storage, rotation, access logging                       | 1–5   |
| Cost / Abuse Control     | Rate limit, quota, max step, budget guardrail                           | 1–5   |

---

## 13. Final Security Principles

1. Assign a unique identity per Agent.
2. Apply least privilege per Agent.
3. Clearly distinguish between user-delegated actions and Agent-owned actions.
4. Do not connect external MCPs directly — place a Gateway in between.
5. Allow MCP servers on an allowlist basis only.
6. Leave all Tool calls in audit logs.
7. Require human approval for high-risk actions such as Write / Delete / Send.
8. Prompts, responses, tool inputs, tool outputs, memory, and RAG context are all subject to security inspection.
9. Do not place Guardrails only before and after the model — they must also be placed at Tool and Memory boundaries.
10. Treat external documents, emails, webpages, tickets, and MCP output all as untrusted content.
11. Indirect prompt injection is one of the most critical threats in RAG and Agent environments.
12. Code execution and Browser Tools must always apply sandbox, network restriction, and audit logging.
13. DLP must be applied not only to prompts/responses but also to file uploads, connector data, tool outputs, and memory.
14. Guardrail detection results must be connected not just to blocking but also to audit logs and SIEM alerts.
15. Agents in production must undergo continuous red teaming and evaluation.
16. Only allow approved MCP Servers, and include tool schemas and descriptions as subjects of security review.
17. Filter Agent memory to prevent storage of passwords, tokens, SSNs, personally identifiable information, and sensitive business data.
18. Restrict domains, APIs, and MCP servers that an Agent can call externally on an allowlist basis.
19. Manage Tool credentials in Secrets Manager or Key Vault.
20. All security events must be reconstructible later in the "User → Agent → Tool → Data → Response" flow.

---

## 14. Final Summary

AI platform security should be viewed through three layers.

### 14.1 Access Security

Who can access what?

Key elements:

* User identity
* Agent identity
* Delegated access
* IAM / RBAC
* Connector permission
* MCP permission

### 14.2 Content Security

What passes between the model and the user?

Key elements:

* Guardrails
* Content filtering
* Prompt injection detection
* Jailbreak detection
* DLP
* PII masking
* Groundedness detection
* Protected material detection

### 14.3 Runtime Security

What can the Agent actually execute?

Key elements:

* Tool allowlist
* MCP Gateway
* Sandbox
* Code execution control
* Browser isolation
* Egress control
* Human approval
* Observability
* Audit trail

Ultimately, the core question of enterprise AI security transforms into:

> Under what permissions does the Agent view what data, execute which Tools, what content guardrails and runtime controls apply throughout that process, and can those actions be audited afterward?

---

## 15. Reference

<a id="OpenAI_Privacy"></a>
[1] [OpenAI — Business data privacy, security, and compliance](https://openai.com/business-data/)  
<a id="OpenAI_Enterprise_Tools"></a>
[2] [OpenAI — New tools for ChatGPT Enterprise compliance, SCIM, and GPT controls](https://openai.com/index/new-tools-for-chatgpt-enterprise/)  
<a id="Anthropic_Compliance_API"></a>
[3] [Anthropic Claude Docs — Compliance API](https://platform.claude.com/docs/en/manage-claude/compliance-api)  
<a id="Anthropic_Data_Retention"></a>
[4] [Anthropic Claude Docs — API and data retention](https://platform.claude.com/docs/en/manage-claude/api-and-data-retention)  
<a id="Anthropic_Compliance_Integration"></a>
[5] [Claude Support — Compliance API integrations](https://support.claude.com/en/articles/15167101-get-started-with-claude-compliance-api-integrations)  
<a id="Google_Gemini_Enterprise_Security"></a>
[6] [Google Workspace Blog — Enterprise security controls for Gemini in Google Workspace](https://workspace.google.com/blog/ai-and-machine-learning/enterprise-security-controls-google-workspace-gemini)  
<a id="Google_Gemini_Audit_Logging"></a>
[7] [Google Cloud Docs — Gemini Enterprise audit logging](https://docs.cloud.google.com/gemini/enterprise/docs/audit-logging)  
<a id="AWS_AgentCore"></a>
[8] [AWS — Amazon Bedrock AgentCore](https://aws.amazon.com/bedrock/agentcore/)  
<a id="AWS_AgentCore_Gateway"></a>
[9] [AWS Docs — Amazon Bedrock AgentCore Gateway](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway.html)  
<a id="AWS_AgentCore_Policy"></a>
[10] [AWS Docs — Policy in Amazon Bedrock AgentCore](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/policy.html)  
<a id="AWS_AgentCore_Observability"></a>
[11] [AWS Docs — AgentCore Observability](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/observability-configure.html)  
<a id="AWS_Bedrock_Guardrails"></a>
[12] [AWS Docs — Amazon Bedrock Guardrails](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails.html)  
<a id="AWS_Guardrails_Prompt_Attack"></a>
[13] [AWS Docs — Detect prompt attacks with Amazon Bedrock Guardrails](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-prompt-attack.html)  
<a id="MS_Purview_Audit_Copilot"></a>
[14] [Microsoft Learn — Audit logs for Copilot and AI applications](https://learn.microsoft.com/en-us/purview/audit-copilot)  
<a id="MS_Foundry_Agent_Identity"></a>
[15] [Microsoft Learn — Agent identity concepts in Microsoft Foundry](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/agent-identity)  
<a id="MS_Foundry_MCP_Gateway"></a>
[16] [Microsoft Learn — Govern MCP tools by using an AI gateway](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/governance)  
<a id="MS_Prompt_Shields"></a>
[17] [Microsoft Learn — Prompt Shields in Azure AI Content Safety](https://learn.microsoft.com/en-us/azure/ai-services/content-safety/concepts/jailbreak-detection)  
<a id="MS_Content_Safety"></a>
[18] [Microsoft Azure — Azure AI Content Safety](https://azure.microsoft.com/en-us/products/ai-services/ai-content-safety)
