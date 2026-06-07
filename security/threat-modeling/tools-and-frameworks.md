# Useful tools and frameworks for threat modeling

## Overview

| Purpose                           | Key References |
| --------------------------------- | -------------- |
| Threat classification             | STRIDE, ATLAS  |
| Attack technique identification   | ATT&CK, CAPEC  |
| Weakness identification / scoring | OWASP Top 10, CWE, CWSS |
| Vulnerability identification / scoring | CVE, CVSS      |
| Application security verification | OWASP ASVS     |
| Risk assessment                   | DREAD, FAIR    |
| AI vulnerability scoring          | AIVSS          |
| AI attack risk scoring            | AARS           |

> **Q.** Difference between **attack identification** and **weakness taxonomy**?  
> **A.** Attack technique identification (ATT&CK, CAPEC) takes the **attacker's perspective** — it describes *what attackers do* (e.g., "an attacker uses SQL injection to exfiltrate data"). Weakness taxonomy (CWE) takes the **defender's perspective** — it describes *what flaws exist in code or design* (e.g., "CWE-89: improper neutralization of SQL input"). The same SQL injection can appear in both: CAPEC-66 describes the attack technique, while CWE-89 classifies the underlying weakness.

```
[Threat classification]                  STRIDE, ATLAS
        ↓
[Attack technique identification]        ATT&CK, CAPEC
        ↓
[Weakness identification / scoring]      OWASP Top 10, CWE → CWSS
        ↓
[Vulnerability identification / scoring] CVE → CVSS
        ↓
[Application security verification]      OWASP ASVS
        ↓
[Risk assessment]                        DREAD, FAIR
        ↓
[AI vulnerability scoring]               AIVSS
        ↓
[AI attack risk scoring]                 AARS
```

## OWASP Top 10
OWASP (Open Worldwide Application Security Project) is a nonprofit organization focused on improving software security. They publish the OWASP Top 10, which is a widely recognized list of the most critical security risks to applications. The OWASP Top 10 is updated periodically to reflect the evolving threat landscape and emerging attack vectors.

> OWASP originally stands for Open Web Application Security Project, but it has since expanded to cover a wide range of security topics beyond just web applications.

| List | Latest | Focus |
| ---- | ------ | ----- |
| [Web Application](https://owasp.org/www-project-top-ten/) | 2021 | Web app security |
| [API](https://owasp.org/API-Security/editions/2023/en/0x11-t10/) | 2023 | API security |
| [LLM](https://owasp.org/www-project-top-10-for-large-language-model-applications/) | 2025 | LLM application security |
| [Mobile](https://owasp.org/www-project-mobile-top-10/) | 2024 | Android/iOS app security |
| [CI/CD Security Risks](https://owasp.org/www-project-top-10-ci-cd-security-risks/) | 2022 | CI/CD pipeline security |
| [Kubernetes](https://owasp.org/www-project-kubernetes-top-ten/) | 2022 | Kubernetes cluster security |
| [Low-Code/No-Code](https://owasp.org/www-project-top-10-low-code-no-code-security-risks/) | 2022 | No-code/low-code platform risks |
| [Privacy Risks](https://owasp.org/www-project-top-10-privacy-risks/) | 2021 | Privacy & data protection |

## OWASP Top 10 - Web Application

Latest version: **2021**

| ID  | Name |
| --- | ---- |
| A01 | Broken Access Control |
| A02 | Cryptographic Failures |
| A03 | Injection |
| A04 | Insecure Design |
| A05 | Security Misconfiguration |
| A06 | Vulnerable and Outdated Components |
| A07 | Identification and Authentication Failures |
| A08 | Software and Data Integrity Failures |
| A09 | Security Logging and Monitoring Failures |
| A10 | Server-Side Request Forgery (SSRF) |

## OWASP Top 10 - API

Latest version: **2023**

| ID      | Name |
| ------- | ---- |
| API1    | Broken Object Level Authorization |
| API2    | Broken Authentication |
| API3    | Broken Object Property Level Authorization |
| API4    | Unrestricted Resource Consumption |
| API5    | Broken Function Level Authorization |
| API6    | Unrestricted Access to Sensitive Business Flows |
| API7    | Server Side Request Forgery |
| API8    | Security Misconfiguration |
| API9    | Improper Inventory Management |
| API10   | Unsafe Consumption of APIs |

## OWASP Top 10 - LLM

Latest version: **2025**

| ID     | Name |
| ------ | ---- |
| LLM01  | Prompt Injection |
| LLM02  | Sensitive Information Disclosure |
| LLM03  | Supply Chain |
| LLM04  | Data and Model Poisoning |
| LLM05  | Improper Output Handling |
| LLM06  | Excessive Agency |
| LLM07  | System Prompt Leakage |
| LLM08  | Vector and Embedding Weaknesses |
| LLM09  | Misinformation |
| LLM10  | Unbounded Consumption |

## OWASP ASVS (Application Security Verification Standard)

## Full suite
### Microsoft Threat Modeling Tool

### OWASP Threat Dragon

### PyTM (Python Threat Modeling)

## References

- [OWASP Top 10 - Web Application (2021)](https://owasp.org/www-project-top-ten/): Top 10 most critical security risks for web applications.
- [OWASP Top 10 - API (2023)](https://owasp.org/API-Security/editions/2023/en/0x11-t10/): Top 10 API security risks.
- [OWASP Top 10 - LLM (2025)](https://owasp.org/www-project-top-10-for-large-language-model-applications/): Top 10 security risks for LLM-based applications.
- [OWASP Mobile Top 10 (2024)](https://owasp.org/www-project-mobile-top-10/): Top 10 security risks for mobile applications.
- [OWASP Top 10 CI/CD Security Risks (2022)](https://owasp.org/www-project-top-10-ci-cd-security-risks/): Top 10 security risks in CI/CD pipelines.
- [OWASP Kubernetes Top 10 (2022)](https://owasp.org/www-project-kubernetes-top-ten/): Top 10 security risks for Kubernetes environments.
- [OWASP Top 10 for Low-Code/No-Code (2022)](https://owasp.org/www-project-top-10-low-code-no-code-security-risks/): Top 10 risks for low-code and no-code platforms.
- [OWASP Top 10 Privacy Risks (2021)](https://owasp.org/www-project-top-10-privacy-risks/): Top 10 privacy risks for web applications.