---
tags: [pv080, lecture, gdpr, privacy, ethics, law]
lecture: 7
---

# L07 – Personal Data Protection, GDPR & Ethics

> Full notes: [[../fulltext|fulltext]]

## Privacy vs. Personal Data Protection

- **Privacy** = space for unique identity, private opinion, dissent
- **Personal data protection** = rules for data operations with risks/harms to individuals
  - Harms: invasion of privacy, manipulation via profiling, discrimination, extortion, identity theft
- Key instrument: **GDPR** (Regulation 2016/679)

## GDPR Core Terminology

See [[../concepts/GDPR|GDPR]] for full details.

| Term | Definition |
|------|-----------|
| **Personal data** | Any info about an identified or **identifiable** natural person; contextual concept |
| **Controller** | Entity that **initiates** processing |
| **Processor** | Entity that **carries out** processing at controller's initiative |
| **Processing** | Any structured or (semi)automated operation on personal data |

## Processing Principles (Article 5)

1. Lawful, fair, transparent
2. Purpose limitation
3. Data minimisation
4. Accuracy
5. Storage limitation
6. Integrity and confidentiality
7. **Accountability** — must demonstrate compliance

## Legal Bases (Article 6)

Contract | Legal obligation | Legitimate interests | Public interest | Vital interests | **Consent**

## Sensitive Personal Data (Articles 9–10)

**Default: prohibited.** Exceptions require explicit consent or special grounds.

Sensitive categories include: racial/ethnic origin, political opinions, religious beliefs, trade union membership, **genetic/biometric data**, **health data**, sexual orientation, criminal records.

## Data Subject Rights

| Right | Article |
|-------|---------|
| Right to information | Art. 13–14 |
| Right of access | Art. 15 |
| Right to rectification | Art. 16 |
| Right to erasure ("to be forgotten") | Art. 17 |
| Right to object | Art. 21 |

## Controller's Duties

**Part I:** SCOPE → PURPOSE → BASIS → TRANSPARENCY → PROPORTIONALITY → DOCUMENTATION  
**Part II:** TRAINING → SECURITY → INCIDENT HANDLING → CONTACT POINT

## Cross-Border Transfers

- Within EU: free movement
- Outside EU: EC adequacy decision OR standard contractual clauses OR binding corporate rules OR explicit consent

## Fines

Must be effective, proportionate, dissuasive. Usually preceded by corrective measure.  
**Largest Czech fine: Avast (April 2024) — €13.7 million** — failed to anonymize browsing data of >100 million users.

## Privacy Techniques

### Data Aggregation

- Combining data from multiple datasets may raise sensitivity level
- Example: age + insurance change + specialist visit → inference of serious illness
- GDPR strongly against large-scale aggregation

### Inference

- Obtaining higher-sensitivity information from lower-sensitivity data
- **Statistical database attack:** Dorothy Denning's boss salary revealed via crafted aggregate queries

### Statistical Database Protections

| Technique | How it works |
|-----------|-------------|
| **Perturbation** | Adding noise to query results |
| **Minimum query scope** | Require minimum number of matching records |
| **Maximum query scope** | Limit queries affecting too large a fraction |
| **Random sampling** | Return random sample instead of all matching records |

**Critical query** = query that reveals information about one specific individual.  
**Positive compromise** = query series yields exactly 1 record.  
**Partial compromise** = result is 0 or 1 (reveals membership).

## Computer Ethics

### Motivating Behavior (Don Parker's 3 Powers)
1. **Power of law and regulations** — fear of punishment
2. **Power of conventions** — professional codes of conduct
3. **Moral settings** — customs, social experience

### Two Practical Ethical Questions
1. If I do this to somebody, would I mind if somebody did this to me (or someone close)?
2. Before acting, ask those who will be impacted (or their representatives)?

## ACM Code of Ethics (Key Points)

**1. General Principles:** contribute to society, avoid harm, be honest, be fair, respect privacy, honor confidentiality

**2. Professional Responsibilities:**
- Maintain competence and ethical practice
- Design systems that are **robustly and usably secure** (2.9)

**3. Professional Leadership:** public good as central concern; take special care of infrastructure systems.

## Key Points

- **Personal data** = any info about an identified or identifiable natural person; contextual
- **Controller** initiates; **processor** executes; employees follow instructions
- **6 lawful bases:** contract, legal obligation, legitimate interest, public interest, vital interests, consent
- **Sensitive data** = default prohibition + explicit consent/special grounds required
- **Art. 5 principles:** lawful/fair/transparent, purpose limitation, minimisation, accuracy, storage limit, security + accountability
- **Inference + aggregation** can raise sensitivity level; statistical database attacks
- **ACM Code 2.9:** design systems robustly and **usably** secure

## Related

- [[../concepts/GDPR|GDPR]]
- [[../concepts/Privacy-Anonymity|Privacy & Anonymity]]
- [[L06-Privacy|L06 – Privacy]]
- [[L08-ComputerSecurity-Intro|L08 – Computer Security Intro]]
