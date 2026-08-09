# Terms in Security

Glossary of security terms that recur across the notes in this folder. Each entry contrasts a pair of terms that are easily confused.

## List

| Term | Summary |
|------|---------|
| [Permission vs Access Control](#permission-vs-access-control) | A single granted right vs the system that defines and enforces rights |
| [Token](#token) | Access token vs refresh token, their lifetimes and handling |
| [Ingress / Egress](#ingress--egress) | Inbound vs outbound traffic across a network boundary |
| [Unlinkability vs Non-targetability](#unlinkability-vs-non-targetability) | Blocking correlation vs preventing an individual from being singled out |
| [SIEM vs SOC](#siem-vs-soc) | The log aggregation platform vs the team that operates it |

## Permission vs Access Control

| Term | Scope | Focus |
|------|-------|-------|
| Permission (권한) | Single capability | What an identity *can do* on a specific resource or action |
| Access Control (접근 통제) | System-wide mechanism | How permissions are *defined, evaluated, and enforced* |

**Permission (권한)**
- A discrete right granted to a subject: e.g., `read`, `write`, `delete`, `invoke`.
- Exists at the leaf level — the smallest unit of authorization.
- Can be granted directly (user → permission) or indirectly via roles or policies.

**Access Control (접근 통제)**
- The broader system that manages the full lifecycle: identify → authenticate → authorize → enforce → audit.
- Encompasses models (RBAC, ABAC, ReBAC), policies, enforcement points (API gateway, IAM, ACL), and auditing.
- Permission is *one input* to an access control decision.

**Analogy**
- Permission = a key that opens a specific lock.
- Access Control = the entire security system: who gets keys, which doors they open, who monitors entry logs.

> See also: [access-control.md](./access-control.md) for models, principles, and implementation patterns.

## Token

| Term | Lifetime | Purpose |
|------|----------|---------|
| Access Token | Short (minutes–hours) | Prove authorization to access a resource |
| Refresh Token | Long (days–weeks) | Obtain a new access token without re-authenticating |

**Access Token (액세스 토큰)**
- Presented on every API request (e.g., `Authorization: Bearer <token>`).
- Self-contained (JWT) or opaque — resource server validates it directly or via introspection.
- Short-lived to limit the blast radius if stolen.
- Stateless JWTs cannot be revoked mid-life; opaque tokens can be invalidated at the auth server.

**Refresh Token (리프레시 토큰)**
- Stored securely by the client (httpOnly cookie, secure storage) — never sent to the resource server.
- Exchanged at the authorization server's token endpoint for a new access token.
- Long-lived, so must be rotatable and revocable (one-time use refresh token rotation is best practice).
- If stolen, an attacker can silently obtain fresh access tokens until the refresh token is revoked.

**Typical flow**
```
Client → Auth Server : login → [Access Token (1h) + Refresh Token (30d)]
Client → Resource API : request + Access Token
# Access Token expires
Client → Auth Server : Refresh Token → new Access Token (+ new Refresh Token)
```

**Security considerations**
- Rotate refresh tokens on every use (refresh token rotation) to detect token replay.
- Bind tokens to client (sender-constrained / DPoP) to prevent bearer theft.
- Store refresh tokens in httpOnly, Secure, SameSite=Strict cookies or platform secure storage — never in localStorage.
- Short access token TTL + revocation list for high-security contexts.

## Ingress / Egress

Traffic direction relative to a network boundary (host, container, VPC, data center).

| Direction | Korean | Definition | Example |
|-----------|--------|------------|---------|
| **Ingress** | 인그레스 / 외부 수신 | Inbound traffic entering the boundary | External user → API server, Internet → Load Balancer |
| **Egress** | 이그레스 / 외부 송신 | Outbound traffic leaving the boundary | App server → external API, DB → S3 backup |

---

### Ingress

**Why ingress control matters**
- **Unauthorized access (무단 접근)**: Exposed services reachable by attackers without authentication.
- **DDoS / volumetric attacks**: Flooding inbound traffic exhausting bandwidth or compute.
- **Vulnerability exploitation**: Attackers probing public-facing services for known CVEs.

**Ingress controls**
- **Firewall / Security Group**: Allow only expected ports/protocols from permitted source CIDRs; deny all else by default.
- **WAF (Web Application Firewall)**: Inspect HTTP/S requests for OWASP Top 10 patterns (SQLi, XSS, etc.) at the edge.
- **API Gateway / Load Balancer**: Single entry point with TLS termination, rate limiting, and authentication enforcement.
- **Network policy (Kubernetes)**: `NetworkPolicy` ingress rules restrict which pods/namespaces can send traffic to a pod.
- **DDoS protection**: AWS Shield, Cloudflare — absorb volumetric attacks before they reach the application tier.

**Common patterns**
- Public subnet holds only the load balancer; application and DB tiers live in private subnets with no direct inbound path.
- Geo-blocking or IP reputation filtering at the CDN/WAF layer to drop known-malicious sources.
- mTLS at service mesh ingress to enforce identity-based access between internal services.

---

### Egress

**Why egress control matters**
- **Data exfiltration (데이터 유출)**: Malware or compromised workloads calling attacker-controlled endpoints to leak data.
- **C2 (Command & Control)**: Malicious process phoning home for instructions.
- **Supply-chain pivoting**: Compromised dependency pulling additional payloads at runtime.

**Egress controls**
- **Allowlist-based firewall / Security Group**: deny-by-default outbound, permit only known destinations (IP/FQDN, port, protocol).
- **Egress proxy (송신 프록시)**: Route all outbound HTTP/S through an inspecting proxy (e.g., Squid, Zscaler) — enables URL filtering and TLS inspection.
- **DNS filtering**: Block resolution of known-malicious or uncategorized domains before a connection is established.
- **Network policy (Kubernetes)**: `NetworkPolicy` egress rules restrict pod-to-external communication.
- **Service mesh**: Istio/Envoy `ServiceEntry` + `Sidecar` to declare permitted external services.

**Common patterns**
- Egress-only internet gateway (AWS) for private subnets that need outbound but must not be reachable inbound.
- FQDN-based egress rules (rather than IP) to handle CDN / SaaS with dynamic IPs.
- Alert on unexpected egress destinations as an anomaly detection signal.

## Unlinkability vs Non-targetability

Both are core privacy properties in anonymous communication systems and privacy-by-design engineering.

| Property | Focus | Key Question |
|---|---|---|
| Unlinkability (연결불가능성) | Blocking correlation between data items or actions | "Can these two actions be linked to the same person?" |
| Non-targetability (비표적화성) | Preventing identification/tracking of a specific individual | "Can this person be singled out as a target?" |

**Unlinkability (연결불가능성)**
- Ensures that two or more items (actions, messages, or identifiers) cannot be correlated with each other or traced back to the same individual.
- An observer cannot determine whether two events or messages originate from the same user.
- **LINDDUN** identifies Linkability as one of its seven privacy threat categories; unlinkability is the corresponding countermeasure.
- Example: In Tor, traffic is routed through multiple relays so that no single party can link the sender to the receiver.

**Non-targetability (비표적화성)**
- Makes it difficult for an adversary to single out and surveil a specific individual, even if mass observation is possible.
- The larger the anonymity set, the stronger the non-targetability.
- Related concepts: k-anonymity, l-diversity.
- Example: In a well-designed anonymous system, targeting one person among millions requires prohibitive cost or effort.

Both properties are central to privacy by design and are frequently paired in anonymous communication protocols such as Tor and mix networks.

## SIEM vs SOC

| Term | Nature | Role |
|------|--------|------|
| SIEM (보안 정보 및 이벤트 관리) | Platform / tool | Aggregates logs from the whole organization, correlates them, and raises alerts |
| SOC (보안 관제 센터) | Organization / team | Operates the SIEM around the clock, triages alerts, and drives incident response |

**SIEM (Security Information and Event Management)**

The name is the merger of two older product categories.

- **SIM (Security Information Management)**: collect, normalize, and retain logs for long-term search and reporting.
- **SEM (Security Event Management)**: correlate events in real time and raise alerts.

**Pipeline**

| Stage | Description |
|-------|-------------|
| Collect | Ingest logs from firewalls, servers, endpoints, IdP, SaaS, and cloud audit trails |
| Normalize | Convert vendor-specific formats into a common schema |
| Correlate | Join events across sources with rules (e.g., 20 failed VPN logins, then a success, then a bulk download) |
| Alert | Emit an alert when a rule matches and route it to the SOC |
| Investigate | Reconstruct a timeline from retained logs after an incident |
| Report | Produce audit reports for compliance regimes (PCI-DSS, ISMS, etc.) |

Common products: Splunk, Microsoft Sentinel, Google Chronicle, Elastic Security, Datadog Cloud SIEM.

**SOC (Security Operations Center)**

- The human side: analysts watching SIEM output 24/7, tiered as L1 triage, L2 investigation, L3 threat hunting.
- Consumes SIEM alerts and often drives SOAR (Security Orchestration, Automation and Response) playbooks for automated containment.
- A SIEM without a SOC is a log archive; a SOC without a SIEM has no visibility. The two are deployed together.
