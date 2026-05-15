---
tags: [pv080, lecture, computer-security, threat-modeling]
lecture: 8
---

# L08 – Computer Security: Introduction

> Full notes: [[../fulltext|fulltext]]

## Terminology

| Term | Definition |
|------|-----------|
| **Computer security** | Protecting computer-related assets from unauthorized actions and their consequences |
| **Information security** | Protecting information/data (broader than computer assets, narrower scope) |
| **Cybersecurity** | Protecting broader-defined systems from unauthorized actions |

**Scope of computer security:** data, hardware, software, communication networks, physical devices they control — from **intentional misuse** by unauthorized parties. NOT unintentional damage or reliability failures.

## Six Computer Security Goals

| Goal | Supporting mechanisms |
|------|----------------------|
| **Confidentiality** | Access control + encryption; also procedural (physical access) |
| **Integrity** | Access controls + cryptographic algorithms; error detection/correction |
| **Authorization** | Access control mechanisms (physical devices, services, data) |
| **Availability** | Protection from DoS; resource reservation |
| **Authentication** | Entity auth supports authorization; data origin auth implies integrity |
| **Accountability** | Transaction evidence/logs; digital evidence → supports non-repudiation |

**Principals** = agents representing users, communicating entities, or system processes; have associated **privileges**.

## Trusted vs. Trustworthy

| | Meaning |
|--|---------|
| **Trusted** | Has our confidence (whether deserved or not); relied upon; if it fails, all guarantees lost |
| **Trustworthy** | *Deserves* confidence; will reliably meet expectations |

**Trusted ≠ Trustworthy.** Trust also means: the chance a system can breach the security policy **without leaving any trace of evidence**.

## Security Taxonomy

| Term | Definition |
|------|-----------|
| **Asset** | Information, software, hardware, services — what we protect |
| **Vulnerability** | Weakness: design flaw, implementation flaw, deployment/config issue |
| **Security policy** | What is and is not allowed; specifies assets, users, services, controls |
| **Violation** | System moved into unauthorized (non-secure) state |
| **Attack** | Deliberate execution of steps intended to cause a security violation |
| **Threat** | Combination of circumstances and entities that might harm assets |
| **Adversary** | Source/threat agent behind a potential attack |
| **Attack vector** | Specific method/steps by which attacks are carried out |
| **Countermeasures** | Provided to **prevent** violations |
| **Controls** | Provided to **detect** violations |

**No security policy = no security.**

## Attacker Model

**Five adversary attributes:**
1. **Objectives** — suggest target assets
2. **Methods** — anticipated attack techniques
3. **Capabilities** — computing resources, skills, knowledge, physical access
4. **Funding level** — determines determination and capabilities
5. **Outsider vs. insider** — insider has knowledge advantage and lower-level authorization

**No attacker model = no real need of security** (you don't know what to defend against).

### Adversary Classes

| Class | Name | Characteristics |
|-------|------|----------------|
| **Class 0** | Script kiddies | No system knowledge; exploit existing tools |
| **Class 1** | Clever outsiders | Intelligent but insufficient system knowledge |
| **Class 1.5** | Well-equipped outsiders | Basic knowledge; low-cost equipment |
| **Class 2** | Knowledgeable insiders | Specialized education; access to sophisticated tools |
| **Class 3** | Well-funded organizations | Teams of specialists; most sophisticated tools |

### Classical Attacker Models

**Needham & Schroeder (1978):** Attacker can eavesdrop and interfere with all communication; node internals are safe.  
**Dolev & Yao (1983):** Adversary can overhear, intercept, and synthesize any message, limited only by cryptographic constraints. "The attacker carries the message."

## STRIDE Threat Modeling

| Letter | Threat | Description |
|--------|--------|-------------|
| **S** | Spoofing | Impersonating a thing or entity |
| **T** | Tampering | Unauthorized altering of code/data/packets |
| **R** | Repudiation | Denying responsibility for past actions |
| **I** | Information disclosure | Unauthorized release of data |
| **D** | Denial of service | Impairing availability via resource consumption |
| **E** | Escalation of privilege | Gaining higher access via vulnerabilities |

**Threat model quality:** mismatch between model and reality → **dangerously false sense of security**.  
Two major errors: (1) invalid assumptions (misplaced trust), (2) focus on wrong threats.

## Security Evaluation

| | Formal Security Evaluation | Penetration Testing |
|--|---------------------------|---------------------|
| **Who** | Third-party lab | Hired consultants |
| **Result** | Verified conformance with standards | Demonstrated exploits on live systems |
| **Cost/time** | Considerable | Lower |
| **Recertification** | Required for smallest changes | Not mandatory |

## 3 + 1 Key Questions for Security Analysis

1. What **assets** are valuable?
2. What **potential attacks** put them at risk?
3. How can damaging actions be **stopped or managed**?
4. What is your **threat model**?

## Key Points

- **6 security goals:** confidentiality, integrity, authorization, availability, authentication, accountability
- **Trusted ≠ Trustworthy** — trusted system may fail; trust also = chance of undetected policy breach
- **No security policy = no security**
- **Attacker model must be specified** — without one, you don't know what to defend
- **Dolev-Yao:** attacker carries messages, limited only by cryptographic constraints
- **STRIDE:** Spoofing, Tampering, Repudiation, Info disclosure, DoS, Escalation

## Related

- [[../concepts/Risk-Assessment|Risk Assessment]]
- [[L09-SecurityPolicy-Risk|L09 – Security Policy & Risk]]
