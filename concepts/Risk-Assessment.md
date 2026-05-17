---
tags: [pv080, concept, risk, security-policy]
---

# Risk Assessment

> Related lectures: [[../lectures/L09-SecurityPolicy-Risk|L09]]

## Risk Definition

**Risk** = expected loss due to harmful future events, relative to defined assets over a **fixed time period**.

**Risk formula:** **R = T · V · C**
- **T** = threat information (probability threats are instantiated)
- **V** = existence of vulnerabilities
- **C** = cost/impact of successful attack (asset value; includes intangible costs like reputation)

Equivalently: **R = P · C** where **P = T · V**

## Risk Assessment Approaches

| Approach | Method | Output | Notes |
|----------|--------|--------|-------|
| **Quantitative** | Compute numerical estimates (in $) | Numbers / monetary values | Good only for frequent incidents with historical data; poor for rare/novel attacks |
| **Qualitative** | Ratings and comparative reasoning; 3–5 levels | Risk levels (Low/Medium/High) | More practical; partially automatable |

**ALE (Annual Loss Expectancy) — Quantitative:**

**ALE = Σ Fᵢ · Cᵢ**
- **Fᵢ** = annualized frequency of events of type i
- **Cᵢ** = average loss per occurrence of type i

### ALE Drawbacks

- Estimates based on outdated observations; needs large historical data
- Cannot quantify intangible assets (strategic info, reputation)
- Human attacker behavior unpredictable; motivation evolves
- For rare events, ALE is a **guessing exercise** with little supporting evidence

## Qualitative Risk Assessment

### Steps

1. **Specify assets** to consider
2. **Consider foreseeable threats** (interviews, system settings review)
3. **Review existing controls** and their appropriateness
4. **Estimate likelihood** and **impact** for each threat
5. **Risk score = probability level × impact level**

### Risk Rating Matrix (1–5 levels)

| Impact | P=V.LOW | P=LOW | P=MOD | P=HIGH | P=V.HIGH |
|--------|---------|-------|-------|--------|----------|
| V.LOW | 1 | 1 | 1 | 1 | 1 |
| LOW | 1 | 2 | 2 | 2 | 2 |
| MODERATE | 1 | 2 | 3 | 3 | 3 |
| HIGH | 2 | 2 | 3 | 4 | 4 |
| V.HIGH | 2 | 3 | 4 | 5 | 5 |

## CIS RAM (Center for Internet Security Risk Assessment Method)

- Views impact in **two dimensions**: impact on own institution AND impact on others
- Uses the **higher** of the two impact levels
- A safeguard is "reasonable" if its risk score (burden) is lower than observed risk score

## Principal Questions

1. What assets are most valuable, and what are their values?
2. What system vulnerabilities exist?
3. What are the relevant threat agents and attack vectors?
4. What are the estimates of attack probabilities/frequencies?

## Security Policy Context

Security policy aims to **minimize (manage) risks** by specifying:
- Assets requiring protection
- Allowed users, access means, services, controls

**Bell-LaPadula Model (confidentiality):**
- **NRU (No Read Up):** subject cannot read objects at a higher level
- **NWD (No Write Down):** subject cannot write to objects at a lower level

## ISMS — ISO/IEC 27000

**PDCA Cycle:**

| Phase | Action |
|-------|--------|
| **Plan** | Establish ISMS policy, objectives, processes |
| **Do** | Implement and operate ISMS |
| **Check** | Assess and measure; report to management |
| **Act** | Take corrective/preventive actions; improve |

## Business Continuity

| Metric | Definition |
|--------|-----------|
| **RPO** (Recovery Point Objective) | Max period where data might be permanently lost |
| **RTO** (Recovery Time Objective) | Target time for service restoration after disaster |

## Related

- [[IDS-IPS|IDS/IPS]]
- [[../lectures/L09-SecurityPolicy-Risk|L09 – Security Policy & Risk]]
- [[../lectures/L08-ComputerSecurity-Intro|L08 – Computer Security Intro]]
