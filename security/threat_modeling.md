# Threat Modeling (위협 모델링)

a systematic process for identifying security threats, prioritizing them, and establishing appropriate countermeasures for a system.

## Before we start
- Confusing terms - Threat vs Vulnerability: A **vulnerability** (취약점) is a weakness that *exists* in a system (e.g. unpatched software, missing input validation). A **threat** (위협) is a potential action or event that *exploits* that weakness to cause harm (e.g. an attacker injecting SQL). In short — vulnerability is the flaw; threat is what acts on it. Risk arises when both are present.


## Contents
- Process Overview
- Step 1: Scope Definition (범위 정의)
- Step 2: System Analysis
- Step 3: Threat Identification (위협 식별)
- Step 4: Threat Assessment & Prioritization (위협 평가 및 우선순위 지정)
- Step 5: Mitigation (대응책 도출)
- Step 6: Validation & Documentation (검증 및 문서화)
- Threat Modeling Tools example (위협 모델링 도구 예시)

---

## Process Overview

```
1. Scope Definition (범위 정의)
      ↓
2. System Analysis — Asset Identification / Data Flow Mapping (자산 식별 / 데이터 흐름 파악)
      ↓
3. Threat Identification (위협 식별)
      ↓
4. Threat Assessment & Prioritization (위협 평가 및 우선순위 지정)
      ↓
5. Mitigation (대응책 도출)
      ↓
6. Validation & Documentation (검증 및 문서화)
```

---

## Step 1: Scope Definition (범위 정의)

- Clearly define the target system or feature to be modeled
- Define security objectives — Confidentiality, Integrity, Availability
- Establish Trust Boundaries (신뢰 경계)
- Define stakeholder (이해관계자) and attacker perspectives 

---

## Step 2: System Analysis

### Asset Identification (자산 식별)
- Enumerate data, services, and infrastructure to be protected 
- Examples: user personal data, authentication tokens, DB, API server

### Data Flow Diagram (DFD) Construction (DFD 작성)
- Visualize system components and data flows
- Key elements:
  - **Process**: Component that processes data (calculations, transformations)
  - **Data Store**: DB, files, cache, memory storage
  - **External Entity**: Users, external systems, third-party services
  - **Data Flow**: Data movement path between components (labeled with protocol/method)
  - **Trust Boundary**: Boundary where the security level changes — represents a line between trusted and untrusted zones (e.g., between user input and internal system, between application and external API, between user application and database). Any data crossing this boundary requires validation and security checks. 

> **Q.** Why do we define Trust Boundaries?  
> **A.** Defining trust boundaries helps identify points where security controls are needed to protect sensitive data and prevent unauthorized access.

---

## Step 3: Threat Identification (위협 식별)

### Threat Classification Methods
| Name | Description |
|------|------|
| **STRIDE** | Threat classification model covering **S**poofing, **T**ampering, **R**epudiation, **I**nformation Disclosure, **D**enial of Service, **E**levation of Privilege. |
| **LINDDUN** | Privacy threat modeling framework covering **L**inkability, **I**dentifiability, **N**on-repudiation, **D**etectability, **D**isclosure of information, **U**nawareness, **N**on-compliance. |
| **CIA** | Classic security triad — **C**onfidentiality, **I**ntegrity, **A**vailability. |
| **DIE** | **D**ata, **I**dentity, and **E**nvironment — a model for categorizing threats based on what they target. |
| **PLOT4ai** | AI-specific threat modeling framework covering **P**rivacy, **L**oss of Control, **O**perational, and **T**rust threats in AI systems. |

### STRIDE Model

| Threat Type  | Description | Violated Property |
|-----------|------|-----------|
| **S**poofing (스푸핑) | Impersonating another user or system (다른 사용자/시스템으로 위장) | Authentication (인증) |
| **T**ampering (변조) | Unauthorized modification of data or code (데이터 또는 코드 무단 수정) | Integrity (무결성) |
| **R**epudiation (부인) | Denying responsibility for an action (행위에 대한 책임 부정) | Non-repudiation (부인 방지) |
| **I**nformation Disclosure (정보 노출) | Unauthorized access to sensitive information (민감 정보 무단 접근) | Confidentiality (기밀성) |
| **D**enial of Service (서비스 거부) | Disrupting system availability (시스템 가용성 방해) | Availability (가용성) |
| **E**levation of Privilege (권한 상승) | Gaining unauthorized elevated permissions (허가되지 않은 높은 권한 획득) | Authorization (인가) |

#todo: LINDDUN, CIA, DIE, PLOT4ai models

---
<details>
<summary> More threat types (beyond STRIDE) </summary>

- **Physical Attack (물리적 공격)**: Unauthorized physical access to hardware — theft, device tampering
- **Supply Chain Attack (공급망 공격)**: Compromising software or hardware through a trusted third-party vendor
- **Social Engineering (사회공학)**: Manipulating people into revealing credentials or granting access — phishing, pretexting
- **Insider Threat (내부자 위협)**: Malicious or negligent actions by employees or contractors with legitimate access
- **Zero-Day Exploit (제로데이 익스플로잇)**: Attacking an unknown vulnerability before a patch exists
</details>


### Threat Identification Methods (위협 식별 방법)
- Apply STRIDE to each element in the DFD 
- Construct Attack Trees
- Reference past CVEs and attack patterns — CAPEC, ATT&CK 
- Brainstorming or expert review

> **Q.** Why use a classification framework like STRIDE?  
> **A.** Structured methods provide a systematic way to identify threats across different categories, **ensuring comprehensive coverage**.

> **Q.** Most common approach in practice?  
> **A.** Use a taxonomy such as STRIDE or LINDDUN for baseline threat identification.  
Attach evidence using CAPEC/CWE/CVE references.  
Quantify severity with CVSS.  
Map findings to controls and compliance requirements (for example, NIST and GDPR).

### Common Threats

- **Malware (악성코드)**: Malicious software such as viruses, worms, ransomware, and spyware that can disrupt operations, steal data, or encrypt systems for extortion.
- **Phishing and Social Engineering (피싱 및 사회공학)**: Deceptive messages or interactions that trick users into revealing credentials, sensitive data, or approving harmful actions.
- **Credential Attacks (자격증명 공격)**: Password spraying, brute force, and credential stuffing using leaked or weak passwords.
- **Insider Threat (내부자 위협)**: Intentional abuse or accidental misuse by employees, contractors, or partners with legitimate access.
- **Web Application Attacks (웹 애플리케이션 공격)**: Exploits such as SQL injection, XSS, and CSRF that target application logic and input handling.
- **Denial of Service (서비스 거부 공격, DoS/DDoS)**: Flooding services or exhausting resources to reduce availability.
- **Man-in-the-Middle (중간자 공격, MitM)**: Intercepting or altering communication between parties when transport security is weak or misconfigured.
- **Supply Chain Attack (공급망 공격)**: Compromise through third-party software, libraries, update channels, or service providers.
- **Misconfiguration and Unpatched Systems (오구성 및 미패치 시스템)**: Security gaps caused by insecure defaults, exposed services, or delayed vulnerability patching.


---

## Step 4: Threat Assessment & Prioritization (위협 평가 및 우선순위)

### DREAD Score Model — scored 1~10

| Item | Description|
|------|------|
| **D**amage | Magnitude of damage if attack succeeds (공격 성공 시 피해 규모) |
| **R**eproducibility | Likelihood the attack can be reproduced (공격 재현 가능성) |
| **E**xploitability | Ease of executing the attack (공격 실행의 용이성) |
| **A**ffected Users | Scope of users impacted (영향 받는 사용자 범위) |
| **D**iscoverability | Likelihood of discovering the vulnerability (취약점 발견 가능성) |

**Risk Score (위험도) = (D + R + E + A + D) / 5**

### CVSS (Common Vulnerability Scoring System)
- Industry-standard vulnerability severity scoring system 
- Composed of Base Score, Temporal Score, and Environmental Score

### Priority Matrix (우선순위 매트릭스)

|  | Low Likelihood | High Likelihood  |
|---|:---:|:---:|
| **High Impact** | 🟡 Medium | 🔴 High — Act Immediately |
| **Low Impact** | 🟢 Low | 🟡 Medium |

> **Q.** Criteria for choosing evaluation metrics?  
> **A.** Choose based on **purpose and context**:
> - **DREAD**: Best for internal, team-level threat modeling during early design phases. Quick to apply but subjective — scores depend on the assessor's judgment.
> - **CVSS**: Preferred when standardization matters — compliance reporting, vendor communication, or external audits. More objective and widely recognized, but requires more effort to score accurately.
> - **Priority Matrix (Likelihood × Impact)**: Ideal for executive communication or rapid triage. Provides intuitive visual prioritization without numeric precision.
>
> In practice: use **DREAD or Priority Matrix** for fast internal prioritization, and **CVSS** when formal documentation or cross-organizational comparability is required.

---


## Step 5: Mitigation (대응책 도출)

Choose one of the following four response strategies for each threat:

| Strategy | Description | Example |
|------|------|------|
| **Mitigate (완화)** | Reduce the likelihood or impact of the threat (위협 발생 가능성 또는 영향 감소) | Input validation, applying encryption (입력 검증, 암호화 적용) |
| **Accept (수용)** | Accept the risk when it is low relative to cost (비용 대비 위험이 낮을 때 위험 감수) | Monitor-only for low-risk vulnerabilities (낮은 위험도 취약점 모니터링만 수행) |
| **Transfer (전가)** | Transfer responsibility externally (책임을 외부로 이전) | Insurance, use of external services (보험 가입, 외부 서비스 이용) |
| **Eliminate (제거)** | Remove the feature or asset causing the threat (위협 자체를 유발하는 기능/자산 제거) | Disable unnecessary features (불필요한 기능 비활성화) |

### Common Countermeasures per STRIDE (STRIDE별 일반적인 대응책)

| Threat | Key Countermeasures |
|------|------------|
| Spoofing | Strong authentication — MFA, digital signatures (강력한 인증, 디지털 서명) |
| Tampering | HMAC, digital signatures, access control (디지털 서명, 접근 제어) |
| Repudiation | Audit logs, timestamps (감사 로그, 타임스탬프) |
| Information Disclosure | Encryption (in-transit/at-rest), least privilege (암호화, 최소 권한 원칙) |
| Denial of Service | Rate limiting, redundancy, CDN (이중화) |
| Elevation of Privilege | Least privilege, RBAC, sandboxing (최소 권한 원칙, 샌드박스) |

---

## Step 6: Validation & Documentation (검증 및 문서화)

### Validation (검증)
- Confirm that derived countermeasures actually mitigate threats (도출한 대응책이 실제로 위협을 완화하는지 확인)
- Integrate with security testing — penetration testing, code review (보안 테스트, 코드 리뷰와 연계)
- Assess residual risk (잔여 위험 평가)

### Documentation Items (문서화)
- List of identified threats with STRIDE classification (식별된 위협 목록 및 STRIDE 분류)
- Risk score per threat (위협별 위험도 점수)
- Countermeasures and owners (대응책 및 담당자)
- Review schedule and history (검토 일정 및 이력)

---
## Threat Modeling Tools example (위협 모델링 도구 예시)
### Overview
| Name | Organization | Description |
|------|------|------|
| **Microsoft Threat Modeling Tool** | [Microsoft Learn][1] | Classic DFD-driven desktop tool that auto-generates STRIDE threats and mitigation guidance. |
| **OWASP Threat Dragon** | [OWASP][2] | Diagram-first open-source tool focused on visual threat modeling and attack surface tracking. |
| **IriusRisk** | [iriusrisk.com][3] | Commercial platform with risk, control, and remediation workflows plus some AI-assisted input. |
| **Threagile** | [GitHub][4] | YAML-based threat modeling toolkit for architecture-as-code and report generation. |
| **OWASP pytm** | [OWASP][5] | Python-code-defined threat modeling tool that generates DFDs, sequence diagrams, and threats. |
| **ThreatSpec** | [GitHub][6] | Threat modeling through code comments and annotations for developer-centric workflows. |
| **AWS Threat Composer** | [AWS Documentation][7] | VS Code-based threat modeling experience tailored to AWS documentation and response artifacts. |

### Input & Output
| Name | Input | Output |
|------|------|------|
| **Microsoft Threat Modeling Tool** | DFD, trust boundary, data store, process, external entity | STRIDE-based threat list, mitigation, report |
| **OWASP Threat Dragon** | Diagram, system components, data flow | Threat records, mitigation, attack surface visualization |
| **IriusRisk** | Architecture/components/patterns, some AI-assisted inputs | Risk, control, requirement, remediation workflow |
| **Threagile** | Architecture model written in YAML | Risks/threats, diagrams, hardening recommendations, reports |
| **OWASP pytm** | System model defined in Python code | DFD, sequence diagram, threats |
| **ThreatSpec** | Code comments/annotations | Threat model report, data-flow diagrams |
| **AWS Threat Composer** | Threat model documents/components authored in VS Code | Security issues, response strategy, threat model artifact |

### Tools & Supported Methods

| Tool | Methods | Notes |
|------|------|------|
| **Microsoft Threat Modeling Tool** | STRIDE per Element | Core built-in methodology for generated threats and mitigations. ([1], [8]) |
| **OWASP Threat Dragon** | STRIDE, LINDDUN, CIA, DIE, PLOT4ai | Rule engine can auto-generate threats/mitigations using selected model. ([2], [9]) |
| **IriusRisk** | Methodology-agnostic, custom libraries, framework mapping (e.g., ATT&CK/NIST/PCI DSS/GDPR/FedRAMP) | Supports organization-specific and compliance-driven classification overlays. ([10], [11]) |
| **Threagile** | STRIDE + CWE mapping | Risk categories include STRIDE field and CWE linkage in model/rules/reports. ([4], [12]) |
| **OWASP pytm** | Rule-based custom threat library, CAPEC/CWE/CVE references, CVSS override | Threats are generated from conditional rules; severity/CVSS can be customized per finding. ([5], [13]) |
| **ThreatSpec** | Annotation-based custom taxonomy (e.g., @threat, @mitigates), optional CWE-style tagging | Classification is primarily team-defined via code annotations and report templates. ([6]) |
| **AWS Threat Composer** | STRIDE metadata classification + Priority | Threat entries carry STRIDE labels (S/T/R/I/D/E) and prioritization metadata. ([7], [14]) |

## Other Helpful Tools for Threat Modeling
| Tool | Description |
|------|------|
| **draw.io / Lucidchart** | Diagramming tools for manual DFD creation |

---

## References & Frameworks
- [OWASP Threat Modeling](https://owasp.org/www-community/Threat_Modeling)
- [Microsoft STRIDE](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [CAPEC (Common Attack Pattern Enumeration and Classification)](https://capec.mitre.org/)
- NIST SP 800-154: Guide to Data-Centric System Threat Modeling

[1]: https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool?utm_source=chatgpt.com "Microsoft Threat Modeling Tool overview - Azure"
[2]: https://owasp.org/www-project-threat-dragon/?utm_source=chatgpt.com "OWASP Threat Dragon"
[3]: https://www.iriusrisk.com/?utm_source=chatgpt.com "IriusRisk: AI Threat Modeling Tool for Secure Software ..."
[4]: https://github.com/Threagile/threagile?utm_source=chatgpt.com "Threagile/threagile: Agile Threat Modeling Toolkit"
[5]: https://owasp.org/www-project-pytm/?utm_source=chatgpt.com "OWASP pytm"
[6]: https://github.com/threatspec/threatspec?utm_source=chatgpt.com "threatspec - continuous threat modeling, through code"
[7]: https://docs.aws.amazon.com/toolkit-for-vscode/latest/userguide/threatcomposer.html?utm_source=chatgpt.com "Working with Threat Composer - AWS Toolkit for VS Code"
[8]: https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats "Microsoft Threat Modeling Tool threats (STRIDE model)"
[9]: https://owasp.org/www-project-threat-dragon/ "OWASP Threat Dragon (supports STRIDE/LINDDUN/CIA/DIE/PLOT4ai)"
[10]: https://www.iriusrisk.com/threat-modeling-platform "IriusRisk Threat Modeling Platform"
[11]: https://www.iriusrisk.com/security-content-libraries "IriusRisk Security Content Libraries"
[12]: https://github.com/Threagile/threagile/blob/master/docs/scripts/language-reference.md "Threagile script language reference (STRIDE/CWE fields)"
[13]: https://github.com/izar/pytm "pytm README (threats database, CAPEC/CWE/CVE refs, CVSS override)"
[14]: https://github.com/awslabs/threat-composer "AWS Threat Composer repository (STRIDE metadata)"