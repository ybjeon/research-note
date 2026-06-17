# Agent Landscape

---

## Commercial Agents

| Agent | Category | Developer | Description |
|---|---|---|---|
| **GitHub Copilot Agent** | Coding | Microsoft / GitHub | AI-powered coding assistant in VS Code; supports autonomous multi-step edits, terminal commands, and PR workflows |
| **Claude Code** | Coding | Anthropic | Agentic CLI coding tool that reads, edits, and runs code in a local environment with direct terminal and file system access |
| **OpenAI Codex** | Coding | OpenAI | Cloud-based coding agent that runs in a sandboxed environment; resolves tasks in parallel from natural language instructions |
| **Gemini CLI** | Coding | Google | Open-source terminal AI agent for coding and automation; supports tool use such as shell commands, file operations, and web fetch |
| **Jules** | Coding | Google | Asynchronous coding agent that works with GitHub repositories in a Google Cloud VM and presents plan/reasoning/diff before completion |
| **Devin** | Coding | Cognition AI | Fully autonomous software engineer agent; browses the web, writes and runs code, fixes bugs end-to-end |
| **Cursor Agent** | Coding | Anysphere | Agent mode in the Cursor IDE; applies multi-file edits and runs terminal commands driven by natural language |
| **OpenHands** | Coding | All-Hands AI | Open-source software engineering agent; executes shell commands, edits files, and interacts with browsers in a sandboxed environment |
| **Operator** | Browser | OpenAI | Web-browsing agent that controls a browser to complete real-world tasks (shopping, form filling, etc.) |
| **Claude Computer Use** | Browser | Anthropic | Gives Claude the ability to control desktop GUIs via screenshots and mouse/keyboard actions |
| **AutoGPT** | Workflow | Significant Gravitas (OSS) | One of the first widely adopted autonomous agents; chains LLM calls with memory and tool use |
| **Microsoft Copilot Studio** | Enterprise | Microsoft | Low-code platform for building and deploying custom AI agents ("copilots") in enterprise environments |
| **Amazon Bedrock Agents** | Enterprise | AWS | Managed service for building agents that connect Foundation Models to enterprise data and APIs |

---
## Languages

Client-side implementation languages are not always fully disclosed, but for a subset of notable agents we can summarize publicly known stacks as follows.

| Agent | Developer | Client / Runtime Language |
|---|---|---|
| **Claude Code** | Anthropic | **TypeScript / Node.js** (public CLI distribution and ecosystem integrations) |
| **OpenAI Codex** | OpenAI | **Python + TypeScript/JavaScript** (cloud agent runtime not fully disclosed; SDK/client ecosystems center on Python and JS/TS) |
| **Gemini CLI** | Google | **TypeScript / Node.js** (open-source CLI; repository language is predominantly TypeScript) |
| **Jules** | Google | **Undisclosed** (web product + cloud VM execution model are public, internal implementation language is not fully disclosed) |
| **Operator** | OpenAI | **Undisclosed (likely mixed stack)**; browser automation client APIs are commonly used from Python and JS/TS |

---

## Agent Frameworks

| Framework | Developer | Description |
|---|---|---|
| **LangChain** | LangChain (OSS) | Foundational framework for composing LLM-powered applications; provides chains, agents, tools, memory abstractions, and integrations with hundreds of data sources and model providers |
| **LangGraph** | LangChain (OSS) | Graph-based orchestration framework for stateful, multi-actor agent workflows; built on top of LangChain |
| **AutoGen** | Microsoft Research (OSS) | Multi-agent conversation framework for building cooperative agent systems |
| **CrewAI** | CrewAI (OSS) | Role-based agent orchestration framework focused on collaborative task execution |
| **LlamaIndex Workflows** | LlamaIndex (OSS) | Agent and workflow framework for retrieval-centric applications and tool-using agents |
| **Semantic Kernel** | Microsoft (OSS) | SDK/framework for composing LLM functions, planners, and tool integrations across languages |

---

### LangGraph

LangGraph is a graph-based orchestration framework for building stateful, multi-actor agent workflows on top of LangChain. It models an agent workflow as a directed graph where **nodes** are processing units (LLM calls, tools, or sub-agents) and **edges** define the control flow between them — including conditional branching and cycles.

#### Core Concepts

| Concept | Description |
|---|---|
| **Node** | A single processing unit: an LLM call, a tool execution, or an arbitrary Python function |
| **Edge** | A directed transition between nodes; can be unconditional or conditional (based on state) |
| **State** | A shared, typed object passed through the graph; each node reads and writes to it |
| **Graph** | The overall workflow definition compiled from nodes and edges |
| **Checkpointer** | Persistence layer that saves state at each step, enabling pause/resume and human-in-the-loop |

#### Why LangGraph

- **Cycles over DAGs**: Unlike simple chain-based pipelines, LangGraph supports loops — essential for reflection, retry, and tool-use patterns where an agent may need to revisit a step.
- **Explicit state management**: All inter-node data flows through a single typed state object, making agent behavior easier to reason about and debug.
- **Human-in-the-loop**: The checkpointer allows execution to pause at a node boundary, await human approval or input, and resume — critical for high-stakes workflows.
- **Streaming**: Emits intermediate state updates per node, enabling real-time visibility into long-running agent runs.
- **Multi-agent orchestration**: Supports supervisor and hierarchical patterns where one agent routes tasks to specialized sub-agents.

#### When to Use

- When the agent workflow requires **cycles** (e.g., plan → act → reflect → re-plan)
- When **persistent state** across turns or sessions is needed
- When **human review** must be inserted at specific decision points
- When building **multi-agent systems** with explicit routing logic

#### When Not to Use

- Simple single-pass pipelines (LangChain LCEL or plain function calls are sufficient)
- Prototypes where graph overhead adds unnecessary complexity
