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
