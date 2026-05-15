---
tags: [pv080, concept, gdpr, privacy, law]
---

# GDPR

> Related lectures: [[../lectures/L07-GDPR-Ethics|L07]]

## Overview

**General Data Protection Regulation 2016/679** — EU regulation for personal data protection.  
Applies to controllers and processors operating in the EU or processing data of EU data subjects.

## Core Terminology (Article 4)

| Term | Definition |
|------|-----------|
| **Personal data** | Any information about an identified or **identifiable** natural person (data subject). Contextual concept. |
| **Processing** | Any structured or (semi)automated operation on personal data |
| **Controller** | Entity that **initiates** processing of personal data |
| **Processor** | Entity that **carries out** processing at initiative of another (the controller) |

## Processing Principles (Article 5)

1. **Fair, lawful, and transparent**
2. For **specific, explicit and legitimate purposes** (purpose limitation)
3. **Proportionate** — data minimisation
4. **Accurate** and updated
5. **Storage limitation** — no longer than necessary
6. **Integrity and confidentiality** — appropriate security

**Accountability:** controller is responsible and must be able to **demonstrate** compliance.

## Legal Bases for Processing (Article 6)

- **Contract** — necessary for contract performance
- **Legal obligation** — required by law
- **Legitimate interests** — must balance against data subject rights
- **Public interest / official authority**
- **Vital interests** of data subject
- **Consent** — must be freely given, specific, informed

## Sensitive Personal Data (Articles 9–10)

**Default: prohibition of processing.** Exceptions require explicit consent or special grounds.

Sensitive categories include data about:
- Racial/ethnic origin, political opinions, religious/philosophical beliefs
- Trade union membership
- **Genetic or biometric data for identification**
- **Health data**
- Sexual orientation/life
- Criminal convictions and offences

## Data Subject Rights (Chapter III)

| Right | Article | Description |
|-------|---------|-------------|
| **Right to information** | Art. 13–14 | What data, why, by whom, how obtained |
| **Right of access** | Art. 15 | Info + free copy; must not affect others' rights |
| **Right to rectification** | Art. 16 | Inaccuracies corrected without delay |
| **Right to erasure** | Art. 17 | Deletion — except for expression, legal obligation, archiving, legal claims |
| **Right to object** | Art. 21 | Object to processing for marketing, public interest, legitimate interests |

## Controller's Key Duties

- Identify what is personal data and how it is processed (SCOPE)
- State why data is processed (PURPOSE)
- Identify legal basis; determine if consent is needed (BASIS)
- Maintain records of processing activities (DOCUMENTATION)
- Document security measures (SECURITY)
- Train employees and have incident handling procedures
- Publish a contact point for data subject rights requests

## Cross-Border Transfers (Chapter V)

- Within EU: free movement applies
- Outside EU: requires adequate protection:
  - **EC adequacy decision** (e.g., EU–US Data Privacy Framework)
  - **Standard contractual clauses**
  - Binding corporate rules / explicit consent

## Administrative Fines (Art. 83–84)

- Must be effective, proportionate, dissuasive
- Usually preceded by corrective measure with deadline
- **CZ largest fine: Avast (April 2024) — €13.7 million** — failed to anonymize browsing data of >100 million users

## Data Aggregation & Inference (Privacy Techniques)

**Data aggregation:** merging data from multiple datasets → may raise sensitivity level.  
Example: age + insurance change + specialist visit → inference of serious illness.

**Inference:** obtaining higher-sensitivity information from lower-sensitivity data.

**Statistical database compromise:**
- **Positive compromise** — query series yields exactly 1 individual
- **Partial compromise** — result is 0 or 1, revealing information

Classic case: **Dorothy Denning** revealing her boss' salary via aggregate queries.

**Perturbation technique:** adding noise to query results to prevent precise inference.  
**Minimum query scope technique:** requiring minimum number of records per query.  
**Maximum query scope technique:** limiting queries affecting too large a portion of the database.  
**Random sampling technique:** returning a random sample rather than the full matching set.

## Related

- [[Privacy-Anonymity|Privacy & Anonymity]]
- [[../lectures/L07-GDPR-Ethics|L07 – GDPR & Ethics]]
- [[../lectures/L06-Privacy|L06 – Privacy]]
