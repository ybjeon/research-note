# CVSS (Common Vulnerability Scoring System)

Detailed reference for CVSS, the industry-standard system for scoring vulnerability severity. This document covers the metric groups, scoring formulas, vector string format, and the differences between CVSS v3.1 and v4.0. For how CVSS fits into the broader threat modeling process, see [Threat Modeling](README.md).

## Contents
- Overview
- Severity Rating
- Base Score
- Temporal Score (v3.1)
- Threat Metrics (v4.0)
- Environmental Score
- Vector String
- CVSS v3.1 vs v4.0
- Worked Example
- Usage Notes
- FAQ

## Overview

- Maintained by **FIRST (Forum of Incident Response and Security Teams)**
- Current versions in active use: **v3.1** and **v4.0** (published 2023)
- Produces a single numeric score (0.0 - 10.0) that communicates how severe a vulnerability is, independent of any specific organization
- Composed of up to three metric groups, layered from general to specific:
  - **Base**: intrinsic characteristics of the vulnerability, fixed at disclosure
  - **Temporal (v3.1)** / **Threat (v4.0)**: adjusts for real-world exploit and remediation state
  - **Environmental**: tailors the score to a specific deployment context

## Severity Rating

| Score | Severity |
|-------|----------|
| 0.0 | None |
| 0.1 - 3.9 | Low |
| 4.0 - 6.9 | Medium |
| 7.0 - 8.9 | High |
| 9.0 - 10.0 | Critical |

## Base Score

The Base Score is split into **Exploitability metrics** (how the vulnerability is reached and triggered) and **Impact metrics** (what happens if it succeeds).

### Exploitability Metrics

| Metric | Values | Description |
|---|---|---|
| Attack Vector (AV) | Network (N), Adjacent (A), Local (L), Physical (P) | How remotely the attacker can reach the vulnerable component (공격자가 취약점에 접근 가능한 거리) |
| Attack Complexity (AC) | Low (L), High (H) | Whether conditions beyond the attacker's control must exist for the attack to succeed (공격 성공을 위해 필요한 부가 조건의 유무) |
| Privileges Required (PR) | None (N), Low (L), High (H) | Level of privilege the attacker must hold before the attack (공격 전 필요한 권한 수준) |
| User Interaction (UI) | None (N), Required (R) | Whether a victim user must take some action for the attack to succeed (피해자의 행동 개입 필요 여부) |

### Scope (S) — v3.1 only

- Whether a successful exploit affects resources beyond its own authorization scope (취약점이 자신의 권한 범위를 넘어 다른 컴포넌트에 영향을 주는지 여부)
- **Unchanged (U)**: impact is confined to the vulnerable component itself
- **Changed (C)**: impact extends to a component governed by a different security authority (e.g., a container escape affecting the host)

### Impact Metrics

| Metric | Values | Description |
|---|---|---|
| Confidentiality (C) | None, Low, High | Impact to disclosure of information (기밀성 영향) |
| Integrity (I) | None, Low, High | Impact to trustworthiness/correctness of data (무결성 영향) |
| Availability (A) | None, Low, High | Impact to accessibility of the resource (가용성 영향) |

## Temporal Score (v3.1)

Adjusts the Base Score downward based on the current real-world exploit and patch state. Temporal metrics change over time even though the vulnerability itself does not.

| Metric | Values | Description |
|---|---|---|
| Exploit Code Maturity (E) | Not Defined, Unproven, Proof-of-Concept, Functional, High | Whether a working exploit is publicly available (공개 익스플로잇 존재 여부) |
| Remediation Level (RL) | Not Defined, Official Fix, Temporary Fix, Workaround, Unavailable | Whether an official patch or workaround exists (공식 패치 또는 완화책 여부) |
| Report Confidence (RC) | Not Defined, Unknown, Reasonable, Confirmed | Confidence in the vulnerability report's accuracy (보고 신뢰도) |

## Threat Metrics (v4.0)

CVSS v4.0 replaces the Temporal group with a single **Threat** metric group:

| Metric | Values | Description |
|---|---|---|
| Exploit Maturity (E) | Not Defined, Attacked, POC, Unreported | Observed or expected exploitation activity in the wild (실제 악용 관측 여부) |

## Environmental Score

- Lets the organization consuming the score re-weight Base metrics to reflect its own deployment (조직의 배포 환경에 맞게 재조정)
- Modified Base metrics (e.g., Modified Attack Vector) override the vendor-supplied Base values
- Adds **Security Requirements** (Confidentiality Requirement / Integrity Requirement / Availability Requirement), letting an organization state that, for example, confidentiality matters more than availability for a given asset
- Defined by the organization, not the vendor — this is the layer where asset criticality enters the score

## Vector String

CVSS scores are published alongside a machine-readable vector string that encodes every metric value. This makes scores reproducible and auditable.

```
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H
```

- `CVSS:3.1` — version identifier
- Each subsequent `Metric:Value` pair corresponds to a Base metric
- Temporal and Environmental metrics append to the same string when present, e.g. `/E:F/RL:O/RC:C`

## CVSS v3.1 vs v4.0

| | v3.1 | v4.0 |
|---|---|---|
| Scope metric | Single Scope (S): Unchanged / Changed | Removed — replaced by separate **Vulnerable System** (VC/VI/VA) and **Subsequent System** (SC/SI/SA) impact metrics |
| Attack Requirements | Not present | New metric (AT) — captures execution conditions separate from Attack Complexity |
| Temporal group | Exploit Code Maturity, Remediation Level, Report Confidence | Renamed **Threat** group, simplified to Exploit Maturity (E) only |
| Supplemental metrics | Not present | New optional group — Safety, Automatable, Recovery, Value Density, Vulnerability Response Effort, Provider Urgency |
| Environmental Security Requirements | Present | Present, retained |
| Naming convention | Single "CVSS score" | Named combinations depending on which groups are applied — CVSS-B (Base only), CVSS-BE (+Environmental), CVSS-BTE (+Threat +Environmental) |

> v4.0 was designed to reduce reliance on Scope (a metric widely reported as confusing in v3.1) and to give clearer guidance for OT/ICS and safety-impacting systems via the Supplemental metrics.

## Worked Example

`CVE-2021-44228` (Log4Shell):

```
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H — Base Score 10.0 (Critical)
```

- **AV:N** — exploitable remotely over the network (crafted log input)
- **AC:L** — no special conditions required
- **PR:N / UI:N** — no privileges or user interaction needed
- **S:C** — a logging library compromise can lead to full remote code execution on the host, beyond the logging component's own authorization scope
- **C:H / I:H / A:H** — full compromise of confidentiality, integrity, and availability

## Usage Notes

- CVSS measures **severity**, not **risk** — it does not account for **threat likelihood** or **business context**
- Use the Base Score for vendor advisories and cross-organization comparison
- Use the Environmental Score for internal prioritization aligned with asset sensitivity
- Combine with threat intelligence (e.g., **EPSS**, Exploit Prediction Scoring System) for a more complete risk picture — CVSS says how bad, EPSS estimates how likely it is to be exploited soon

| | Base Score | Temporal / Threat Score | Environmental Score |
|---|---|---|---|
| **What it measures** | Intrinsic severity of the vulnerability itself | Current real-world exploitability and patch state | Severity adjusted to the specific deployment context |
| **Changes over time?** | No — fixed at disclosure | Yes — changes as exploits appear or patches are released | Yes — changes as the environment changes |
| **Who defines it?** | Vendor / researcher | Vendor / threat intel | Consuming organization |
| **Typical use** | Cross-org comparison, vendor advisories, NVD entries | Urgency decisions ("is there a public exploit yet?") | Internal prioritization aligned to asset value |

In practice: start with the **Base Score** for a vendor-neutral severity baseline, apply the **Temporal/Threat Score** to factor in whether a working exploit or patch exists right now, then apply the **Environmental Score** to reflect how critical the affected asset actually is. Each layer narrows from "how bad is this vulnerability in general?" to "how urgent is this for us, today?"

## FAQ

> **Q.** Is a higher CVSS score always a higher priority to fix?  
> **A.** Not necessarily. CVSS Base Score ignores exploitation likelihood and business context. A 9.8 vulnerability in an isolated internal tool with no known exploit can be lower priority than a 6.5 vulnerability in an internet-facing service that is actively being exploited (high EPSS). Use Base Score for severity, then layer Temporal/Threat, Environmental, and threat intelligence for actual prioritization.

> **Q.** Why did v4.0 remove the single Scope metric?  
> **A.** In practice, assessors frequently misapplied Scope: Changed, inflating scores. v4.0 replaces it with explicit Vulnerable System and Subsequent System impact metrics, making the "impact beyond the vulnerable component" concept measurable per C/I/A rather than a single binary flag.

## Reference
<a id="FIRST_CVSS"></a>
[1] [FIRST CVSS](https://www.first.org/cvss/)  
<a id="FIRST_CVSS_v4"></a>
[2] [FIRST CVSS v4.0 Specification](https://www.first.org/cvss/v4-0/)  
<a id="NVD_CVSS"></a>
[3] [NVD CVSS Calculator](https://nvd.nist.gov/vuln-metrics/cvss)  
<a id="FIRST_EPSS"></a>
[4] [FIRST EPSS](https://www.first.org/epss/)
