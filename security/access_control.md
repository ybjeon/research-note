# Access Control

### Access control lifecycle
- **Identify subject (주체 식별)**: Determine who or what is requesting access (user, service account, workload).
- **Authenticate (인증)**: Verify identity using factors such as password, token, certificate, or biometrics.
- **Authorize (인가)**: Decide whether the authenticated subject can perform a requested action.
- **Enforce (강제)**: Apply policy at runtime through gates such as API gateway, service mesh, IAM, database policy, or file ACL.
- **Audit (감사)**: Record who accessed what, when, from where, and with which decision outcome.

### Core models
- **DAC (Discretionary Access Control, 임의 접근 통제)**: Resource owner decides access permissions.
- **MAC (Mandatory Access Control, 강제 접근 통제)**: Central policy and security labels determine access.
- **RBAC (Role-Based Access Control, 역할 기반 접근 통제)**: Permissions are grouped by role and assigned to identities.
- **ABAC (Attribute-Based Access Control, 속성 기반 접근 통제)**: Policy uses attributes (user, resource, action, context).
- **ReBAC (Relationship-Based Access Control, 관계 기반 접근 통제)**: Decisions are made based on graph-like relationships.

### Key principles
- **Least Privilege (최소 권한)**: Grant only the minimum permissions required for tasks.
- **Need to Know (업무 필요 기반)**: Limit data visibility to business necessity.
- **Separation of Duties (직무 분리)**: Split sensitive operations across multiple roles.
- **Default Deny (기본 거부)**: Deny by default and allow explicitly.
- **Just-In-Time Access (적시 권한)**: Use temporary elevation instead of standing privileges.

### Typical implementation patterns
- **Authentication + Authorization split (인증과 인가 분리)**: Keep identity proof and policy decision logically separated.
- **Centralized policy with distributed enforcement (중앙 정책, 분산 강제)**: Manage policy centrally, enforce close to each resource.
- **Policy as code (정책의 코드화)**: Version control and test access rules (e.g., OPA/Rego, Cedar).
- **Context-aware controls (맥락 인지 통제)**: Include device trust, geolocation, risk score, and session signals.

### Common risks and mitigations
- **Over-privileged accounts (과도한 권한 계정)** -> periodic access review, role mining, automated right-sizing.
- **Privilege creep (권한 누적)** -> time-bound access and automatic expiration.
- **Broken object-level authorization (BOLA, 객체 수준 인가 취약점)** -> object ownership checks on every request.
- **Weak service-to-service trust (서비스 간 신뢰 취약)** -> mTLS, short-lived credentials, workload identity.
- **Missing auditability (감사 가능성 부족)** -> immutable logs, trace IDs, and alerting on sensitive actions.

### Practical checklist
- Define resource-action matrix for critical systems.
- Classify identities: human, workload, third-party integration.
- Require MFA for privileged or sensitive operations.
- Implement approval workflow for high-risk access requests.
- Review and recertify permissions on a fixed schedule.
- Monitor denied/allowed patterns for anomaly detection.