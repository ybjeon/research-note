# Basic Security
> **main purpose of this page**
> - offer all the basic knowledge and principles that are necessary to understand security topics
> - include all terms and concepts related to security


## Security goals (보안 목표)

### CIA Triad
- **Confidentiality (기밀성)**: Information is accessible only to authorized users.
- **Integrity (무결성)**: Ensures that information is not altered or tampered with.
- **Availability (가용성)**: Information and systems are accessible when needed.

### Additional Security goals
- **Authentication (인증)**: Verifies the identity of a user or system.
- **Authorization (권한부여)**: Manages access permissions for authenticated users.
- **Non-repudiation (부인방지)**: Provides traceable evidence of actions and responsibility.
- **Accountability (책임성)**: Enables auditing and tracking of user behavior.
- **Privacy (개인정보보호)**: Protects and controls personal information.

## Threat, Vulnerability, and Risk

- **Threat (위협)**: A potential cause of an unwanted incident, such as an attacker, malware, insider misuse, or natural disaster.
- **Vulnerability (취약점)**: A weakness in design, implementation, configuration, or process that can be exploited by a threat.
- **Risk (위험)**: The possibility of loss or damage when a threat exploits a vulnerability, usually evaluated by likelihood and impact.


### Relationship
- A **threat** targets a **vulnerability**, which creates **risk**.
- Without a vulnerability, a threat may not lead to meaningful risk.
- Practical risk evaluation is often modeled as:

$$
Risk \approx Likelihood \times Impact
$$

### Example
- **Threat (위협)**: Credential stuffing attack.
- **Vulnerability (취약점)**: Weak password policy and no MFA.
- **Risk (위험)**: Account takeover causing data exposure and service abuse.

### Risk treatment options
- **Mitigate (완화)**: Reduce likelihood/impact with controls (e.g., MFA, rate limiting, monitoring).
- **Transfer (전가)**: Shift financial impact (e.g., cyber insurance, contractual arrangements).
- **Avoid (회피)**: Stop the risky activity.
- **Accept (수용)**: Keep the risk within approved tolerance.

## Access Control (접근 통제)

### Terms
- **Subject (주체)**: The active entity requesting access, such as a user, process, service account, or device.
- **Object (객체)**: The resource being protected, such as a file, database row, API, secret, or network segment.
- **Action (행위)**: The requested operation on the object, such as read, write, delete, execute, or approve.
- **Authentication (인증)**: The process of verifying the identity of a subject before access is evaluated.
- **Authorization (권한부여)**: The process of determining whether an authenticated subject can perform a specific action on an object.
- **Policy (정책)**: The rule set that defines who can do what under which conditions.
- **Permission (권한)**: An allowed action on a specific object or object group.
- **Role (역할)**: A fixed set of access permissions that one or more principals may assume for a period of time — distinct from a group, which is a list of principals.
- **Privilege (특권)**: A higher-risk permission set, often including administrative or sensitive operations.
- **Context (맥락)**: Additional signals used in a decision, such as time, location, device posture, session risk, or data sensitivity.

### Decision view
- Access control answers the question: **"Can this subject perform this action on this object under the current context?"**
- A decision is usually expressed as **allow**, **deny**, or **allow with conditions**.
- Strong access control depends on both correct identity verification and correct policy evaluation.
- In modern systems, the same access decision may be enforced at multiple layers: application, API gateway, database, and infrastructure.


### Authentication (인증)
- **Goal**: Confirm that a subject is who it claims to be.
- **Typical methods**:
	- **Something you know**: Password, PIN.
	- **Something you have**: OTP token, authenticator app, security key.
	- **Something you are**: Fingerprint, face, other biometrics.
- **MFA (다중요소인증)** combines two or more factors to reduce account takeover risk.
- **Common risks**: Phishing, credential stuffing, weak password reuse, session theft.
- **Good practices**: Strong password policy, phishing-resistant MFA (e.g., FIDO2/WebAuthn), secure session handling, and anomaly detection.

### Authorization (권한부여)
- **Goal**: Decide what an authenticated subject is allowed to do.
- **Decision inputs**: Identity, role/group, resource sensitivity, action type, and runtime context.
- **Common models**:
	- **RBAC (역할 기반 접근통제)**: Permissions are assigned to roles, then roles to users.
	- **ABAC (속성 기반 접근통제)**: Policy evaluates attributes (user/resource/environment).
	- **ReBAC (관계 기반 접근통제)**: Decisions depend on graph relationships between entities.
- **Common risks**: Over-privileged accounts, missing object-level checks (IDOR), policy drift, and privilege escalation.
- **Good practices**: Least privilege, default deny, explicit policy testing, and periodic access reviews.

### Related concepts (기타)
- **Accounting/Auditing (계정추적/감사)**: Record who did what, when, where, and with what result for forensics and compliance.
- **Least Privilege (최소 권한)**: Grant only the minimum permissions required for the task.
- **Separation of Duties (직무 분리)**: Split sensitive workflows across multiple people or systems to reduce fraud and mistake risk.
- **Just-In-Time Access (적시 권한 부여)**: Grant elevated permissions only for a limited time window.
- **Policy Enforcement Point (PEP) / Policy Decision Point (PDP)**:
	- **PEP** intercepts a request and enforces the decision.
	- **PDP** evaluates policy and returns allow/deny (or conditional) decisions.

