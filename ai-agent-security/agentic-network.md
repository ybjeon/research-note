# Agentic network

## Gateway design pattern

### 1. Centralized Gateway/Orchestrator

The Gateway relays every message, so it can:

- Inspect all requests and responses
- Log traffic
- Enforce authentication and authorization
- Modify messages
- Track costs

Most AI Gateway, MCP Gateway, API Gateway, and Agent Router implementations follow this pattern [[1]](#ref1)[[2]](#ref2).

---

### 2. Gateway Used Only for Initial Connection

- The Gateway only sees the connection setup phase
- Message content after that is not visible to the Gateway
- Agents communicate directly at the network level

Common in service discovery and P2P systems [[3]](#ref3).

---

### 3. End-to-End Encrypted Communication

Traffic passes through the Gateway, but:

- The Gateway sees only ciphertext
- Content cannot be decrypted
- Metadata (who communicated with whom) is still visible

Analogous to how Signal's server handles messages [[4]](#ref4).

---

### 4. Most Common Pattern in Multi-Agent LLM Systems

Frameworks like AutoGen, CrewAI, LangGraph, and OpenAI Agents SDK typically use a central orchestrator [[5]](#ref5)[[6]](#ref6):

Even when agents appear to communicate directly, **the central orchestrator** holds all messages in practice. Reasons include:

- Context management (maintains conversation history and can inject additional context or instructions as needed)
- Track costs
- Debugging
- Audit logging
- Retry logic (implement retries and error handling for failed messages) 
- Enforce authentication and authorization
---

### What gateway can see/do?
| Content | 1. Centralized Gateway | 2. Initial Connection Only | 3. End-to-End Encrypted | 4. Central Orchestrator <br>(Multi-Agent LLM) |
|---|---|---|---|---|
| Session establishment | Yes | Yes | Yes | Yes |
| Sender Identity | Yes | Yes | Yes (metadata) | Yes |
| Message Existence | Yes | No | Yes | Yes |
| Message Visibility | Yes | No | No | Yes |
| Message Modification | Yes (e.g. redact sensitive info) | No | No | Yes (e.g. add context) |
| Audit Logging | Yes | Limited (connection events) | Limited (metadata only) | Yes |
| Cost Management | Yes (track per-message costs) | No | No | Yes |
| Access Control | Yes | Limited (connection-level) | No | Yes |
| Context Management | Yes (inject/modify context per session) | No | No | Yes (maintains full conversation history) |
| Retry Logic | Yes | No | Limited (transport-level only) | Yes  |

---

## Reference

<a id="ref1"></a>
[1] [Top 5 MCP Gateways in 2025: The Complete Guide to Enterprise-Ready AI Agent Infrastructure](https://www.getmaxim.ai/articles/top-5-mcp-gateways-in-2025-the-complete-guide-to-enterprise-ready-ai-agent-infrastructure/)

<a id="ref2"></a>
[2] [MCP Gateway: How It Works, Capabilities and Use Cases](https://obot.ai/resources/learning-center/mcp-gateway/)

<a id="ref3"></a>
[3] [Building a Decentralized, Agent-Friendly Communication Ecosystem](https://medium.com/@sendinglabs/building-a-decentralized-agent-friendly-communication-ecosystem-e0a2a36013c9)

<a id="ref4"></a>
[4] [Trustworthy AI Agents: End-to-End Encryption](https://www.sakurasky.com/blog/missing-primitives-for-trustworthy-ai-part-1/)

<a id="ref5"></a>
[5] [Technical Comparison of AutoGen, CrewAI, LangGraph, and OpenAI Swarm](https://ai.plainenglish.io/technical-comparison-of-autogen-crewai-langgraph-and-openai-swarm-1e4e9571d725)

<a id="ref6"></a>
[6] [Multi-Agent Architecture: Patterns, Use Cases & Production Reality](https://www.truefoundry.com/blog/multi-agent-architecture)
