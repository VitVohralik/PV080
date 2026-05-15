---
tags: [pv080, lecture, security-policy, risk, isms]
lecture: 9
---

# L09 – Security Policy, Risk Assessment & Business Security

> Full notes: [[../fulltext|fulltext]]

## Security as a Cyclic Process

1. **Risk assessment** execution
2. **Security policy** definition
3. **Security architecture** design
4. **Security mechanisms** — design and implementation
5. **Monitoring** of security-related events
6. → Loop back to step 1

## Security Policy

**A security policy specifies:**
- Assets requiring protection
- Specific users allowed to access specific assets and the allowed means
- Security services to be provided
- System controls that must be in place

**Purpose:** minimize (manage) risks; present high-level strategies for achieving security.

**Policy levels:**

| Level | Name | Lifetime |
|-------|------|---------|
| High | Company (IT) security policy | 5+ years |
| Lower | System security policy | 2–3 years |

**Typical security policies:** Bell-LaPadula, system access rules, inter-application policies.

### Bell-LaPadula Confidentiality Model (1973)

Multilevel security policy. Labels objects + clearances for subjects.  
Levels: **Top Secret > Secret > Confidential > Public**

**Two core rules:**
1. **Simple security property (NRU):** subject may **not read up** — cannot read objects at higher level
2. **\*-property (NWD):** subject may **not write down** — cannot write to objects at lower level

## Risk & Risk Assessment

See [[../concepts/Risk-Assessment|Risk Assessment]] for full details.

**Risk = T · V · C** (threat probability × vulnerabilities × cost)  
Also: **R = P · C** where P = T · V

**Risk assessment question:** consequences of wrong assessment → wrong controls → assets improperly protected.

### Quantitative vs. Qualitative

| Approach | Output | Key weakness |
|----------|--------|-------------|
| **Quantitative (ALE)** | Monetary values; ALE = Σ Fᵢ · Cᵢ | Unreliable for rare/novel attacks; requires large historical data |
| **Qualitative** | Risk levels (V.LOW to V.HIGH) | More subjective but more practical; partially automatable |

**ALE analysis for rare events = "a guessing exercise with little evidence".**

**Qualitative risk score = probability level × impact level** (matrix 1–5).

**CIS RAM:** views impact in two dimensions (own institution + impact on others); uses higher of the two.

## ISO/IEC 27000 — ISMS

PDCA Cycle:

| Phase | Action |
|-------|--------|
| **Plan** | Establish ISMS policy, objectives, processes |
| **Do** | Implement and operate ISMS |
| **Check** | Assess and measure; report |
| **Act** | Take corrective actions; continual improvement |

## Common Criteria (CC)

**TOE** (Target of Evaluation), **TSF** (TOE Security Functions), **TSC** (TSF Scope of Control)  
**PP** (Protection Profile) — self-standing evaluation document  
**ST** (Security Target) — theoretical aim, claimed security properties  
**EAL** — 7 assurance levels (EAL1 lowest, EAL7 highest)

**CC evaluation result:** certificate + documentation confirming security claims.  
**Challenges:** lack of transparency, expensive, slow.

## FIPS 140-2/3 — Cryptographic Module Validation

- Validates cryptographic modules (algorithms, modules, entropy)
- NIST (US) & Canada; now worldwide; 5000+ certificates total
- MD5 is **NOT** approved
- Requirements include: physical security, module interfaces, OS security, key management

## IT Audit

**Five phases:**
1. Audit planning
2. Documentation and checks of controls
3. Compliance tests ("Is the documentation correct?")
4. Special tests (functional tests in selected areas)
5. Overall assessment

**Internal audit must be independent of the IT division.**

## Business Continuity Planning (BCP)

**BCP:** preventive and reactive actions to keep essential functions running during disasters.

| Metric | Definition |
|--------|-----------|
| **RPO** (Recovery Point Objective) | Max period where data might be permanently lost |
| **RTO** (Recovery Time Objective) | Target time for service restoration after disaster |

Both are service-specific; may require secondary site.

## Case Study: DoTheThing! s.r.o.

**Common security failures:**
- "All can do anything" principle — only works in very small groups
- "Somebody else surely takes care of that" — **never works**
- Individual components carefully selected, but overall construction didn't provide expected security
- No periodic review or updates
- No defined security policy

**The incident:** database backup SW had firewall connection denials for 22 days — nobody noticed. Last working backup was 22 days old.

## Key Points

- **Security is a cyclic 5-step process** — must be repeated iteratively
- **Security policy** must specify assets, users, access means, services, controls
- **Bell-LaPadula: NRU + NWD** — enforces confidentiality in multilevel systems
- **Quantitative (ALE)** unreliable for rare events; **qualitative** more practical
- **CIS RAM** considers two impact dimensions; uses the higher
- **ISO/IEC 27000 ISMS:** Plan–Do–Check–Act cycle
- **IT audit** emphasizes documentation over technology; independent of IT
- **RPO** = max data loss; **RTO** = target service restoration time

## Related

- [[../concepts/Risk-Assessment|Risk Assessment]]
- [[L08-ComputerSecurity-Intro|L08 – Computer Security Intro]]
- [[L10-NetworkAttacks-IDS|L10 – Network Attacks & IDS]]
