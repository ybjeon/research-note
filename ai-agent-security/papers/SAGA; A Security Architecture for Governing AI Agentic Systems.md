# SAGA: A Security Architecture for Governing AI Agentic Systems

Source: https://www.ndss-symposium.org/wp-content/uploads/2026-s869-paper.pdf
Date: 2026-06-30

## Overview

SAGA (Security Architecture for Governing Agentic systems) is a framework proposed by Northeastern University researchers (Georgios Syros, Anshuman Suri, Jacob Ginesin, Cristina Nita-Rotaru, Alina Oprea), published at NDSS 2026. It addresses the gap in user-controlled governance of LLM-based multi-agent systems, providing formal security guarantees, concrete implementation, and evaluation across realistic agentic tasks.

## Key Points

- Existing designs for agent identity, authorization, and delegation are purely theoretical and lack user-controlled management; SAGA fills this gap.
- Introduces a **Provider** (central registry) that manages user and agent identities, enforces access control policies, and facilitates controlled communication setup between agents.
- Users define an **Agent Contact Policy (CP)** per agent, specifying which agents are allowed to initiate contact and their OTK budget (i.e., interaction quota).
- Uses **One-Time Keys (OTKs)** and **Diffie-Hellman key exchange** to derive a shared secret between agent pairs, enabling the receiving agent to issue an encrypted **Access Control Token (ACT)**.
- Agents communicate directly over mutual TLS after the initial token setup; the Provider is not involved in ongoing messages, ensuring scalability.
- Formally verified with **ProVerif** (Dolev-Yao model): proves token secrecy, agent-Provider authentication, and inter-agent authentication.
- Compatible with Google's A2A protocol and Anthropic's Model Context Protocol (MCP).

## Details

### Architecture Components

| Component | Role |
|-----------|------|
| Provider | Central registry; holds User Registry (DU) and Agent Registry (DA); enforces contact policies; distributes OTKs |
| User Registry | Stores user identities and credentials |
| Agent Registry | Stores agent metadata, TLS certs, access control keys, OTKs, and contact policies |
| Access Control Token (ACT) | Encrypted token scoped to a task; contains expiration timestamp and max request count (Qmax) |
| One-Time Keys (OTK) | Ephemeral keys registered by users for their agents; consumed per contact resolution cycle |

### Protocol Flow

1. **User Registration** - via OpenID Connect; user generates signing key pair, obtains CA certificate.
2. **Agent Registration** - user generates TLS credentials, access control key pair (PAC/SAC), and N OTKs; signs all agent metadata; Provider stores and co-signs.
3. **Agent Contact Policy** - declarative rules with pattern matching over agent IDs and OTK budgets; most-specific rule wins.
4. **Agent Communication** - initiating agent B queries Provider for receiving agent A's metadata + one OTK; performs DH handshake with A to derive shared key; A issues ACT encrypted under shared key; B attaches ACT to each subsequent request; token is reused until expired or quota exhausted.

### Cryptographic Primitives

- Signatures: ECDSA / Ed25519 (existential unforgeability under chosen message attack)
- Key exchange: X25519 (Curve25519) ECDH
- Key derivation: HKDF with SHA-256
- Certificates: X.509 PKI

### Threat Model

The Provider is treated as honest-but-curious: it follows protocol logic but may observe agent metadata and traffic patterns. Six adversarial capabilities (C1-C6) are considered:

| Capability | Description |
|------------|--------------|
| C1 | Adversaries can create agents and register them with the Provider. These agents may **deviate from the protocol** when communicating with other agents, or add themselves to a benign agent's contact policy via **social engineering** on users. |
| C2 | A legitimate agent registered with the Provider can be **compromised** by an adversary, e.g., when interacting with external resources (websites, tools installed on user devices). |
| C3 | Adversaries may instruct an agent to **self-replicate** on the same device or another user's device **without registering the child agent** with the Provider; the parent agent can share TLS keys, access control keys, and tokens with the child. |
| C4 | An adversarial agent may **share its TLS public keys, access control keys, and access control tokens** with another adversary-controlled agent, enabling communication with a benign victim agent. |
| C5 | An adversary could attempt a **Sybil attack** by creating agents with multiple identities. |
| C6 | An adversary may **overhear, intercept, and synthesize any message**, limited only by the computational hardness of the cryptographic primitives used (**Dolev-Yao network adversary**). |

### Extensions

- **Fault tolerance** - RAFT/Paxos cluster for the Provider registry (RethinkDB).
- **Scalability** - sharding of the Agent Registry with a load-balancer proxy.
- **Byzantine resilience** - PBFT for full compromise tolerance.
- **Federation** - cross-domain trust via shared certificates (analogous to cross-realm Kerberos).

## Conclusion

- Protocol overhead is negligible: less than 0.6% of end-to-end task completion time, even with geographically distributed agents and Provider.
- Fault-tolerant RAFT replication reduces throughput by only 12-15% (3-node) to 15% (5-node).
- Throughput scales linearly with sharding: 10 sharders yield ~10x throughput, supporting up to 300 million concurrent agents with 24-hour token lifetimes.
- All three agentic tasks (calendar scheduling, expense reporting, collaborative writing) complete successfully with zero impact on task utility.
- SAGA provides the first concrete, evaluated implementation of a user-governed inter-agent security architecture with strong formal guarantees.

## Reference

[1] Georgios Syros et al., "SAGA: A Security Architecture for Governing AI Agentic Systems," NDSS 2026. https://dx.doi.org/10.14722/ndss.2026.230869  
[2] SAGA source code: https://github.com/gsiros/saga  
[3] Full paper with appendices: https://arxiv.org/abs/2504.21034  
[4] Y. Shavit et al., "Practices for governing agentic AI systems," OpenAI, 2023.  
[5] R. Surapaneni et al., "Announcing the Agent2Agent Protocol (A2A)," Google Developers Blog, April 2025.  
