---
tags: [pv080, lecture, standards, common-criteria, phishing, social-engineering]
lecture: 13
---

# L13 – Security Standards, Case Studies & Closing Notes

> Full notes: [[../fulltext|fulltext]]  
> Related concepts: [[../concepts/Risk-Assessment|Risk Assessment]] · [[../concepts/PKI-Certificates|PKI & Certificates]] · [[../concepts/Authentication|Authentication]]

---

## Standardization

Standards are not obligatory unless required by: **law/regulation, regulator/association, or customer**.

| Organization | Scope |
|---|---|
| **ISO / IEC** | International (ISO/IEC joint for security) |
| **ITU** | International (telecom) |
| **IETF** | Internet (RFCs — open, free) |
| **ETSI / CEN / CENELEC** | European |
| **ANSI, NIST** | US national |

**ISO:** 3+ years to develop; standards valid **5 years** then must be reviewed; **charges** for standards.

---

## NIST Standards

### FIPS (Federal Information Processing Standards)

| Standard | Content |
|---|---|
| **FIPS 140-2/3** | Security Requirements for Cryptographic Modules |
| **FIPS 180-4** | Secure Hash Standard (SHA-2) |
| **FIPS 186-4** | Digital Signature Standard (DSS) |
| **FIPS 197** | AES |
| **FIPS 198-1** | HMAC |
| **FIPS 202** | SHA-3 |

### FIPS 140 Security Requirements include:
- Physical security, Module interfaces, OS security
- Services and authentication, Key management
- Methods for mitigating other attacks

**Note:** MD5 is NOT an approved algorithm under FIPS 140.

### SP 800-series (key publications)
- SP 800-30: Risk Assessments
- SP 800-53: Security and Privacy Controls
- SP 800-63: Digital Identity Guidelines (IAL/AAL/FAL)

---

## Common Criteria (CC)

**Standard:** ISO/IEC 15408 | **Version:** v3.1 rev. 5 (2017)  
**Serves:** users, manufacturers, evaluators  
Evaluation requested and **paid for** by vendor; claim is verified and certified or debunked.

### Key Concepts

| Term | Meaning |
|---|---|
| **TOE** | Target of Evaluation — the evaluated product/system |
| **TSF** | TOE Security Functions (HW, SW, FW) |
| **TSC** | TSF Scope of Control |
| **PP** (Protection Profile) | Catalogued evaluation document (e.g., for smartcards) |
| **ST** (Security Target) | The claimed security properties |
| **SFR** | Security Functional Requirement — individual security function |

### EAL (Evaluation Assurance Levels)
- **7 levels: EAL1 (lowest) → EAL7 (highest)**
- Hierarchical — higher levels include all lower requirements
- Result is discrete: **yes/no** per requirement (not a score)

### Challenges
- Lack of transparency, expensive, slow

### Security Evaluation History

| Criteria | Origin | Notes |
|---|---|---|
| **Orange Book (TCSEC)** | USA 1985 | Class D to A1 |
| **ITSEC** | Europe | Split functionality and assurance |
| **Common Criteria** | Worldwide | Current standard |

---

## Case Study 1: DoTheThing! s.r.o.

Small IT company grows → security fails due to systemic mistakes:

| Mistake | Problem |
|---|---|
| AV on individual machines | Not controlling all company communications |
| Shared VPN password | Bypassed by automated external connections |
| Authentication unsynchronized | Not centralized across nodes |
| Backup not monitored | Software failure undetected for **22 days** |
| Logs not analyzed | Sometimes didn't exist at all |

**Root cause:** "all can do everything" + "somebody else takes care of it" = **no policy, no accountability**.

**Correct approach:** cyclic security process: risk assessment → policy → architecture → mechanisms → **monitoring** → loop.

---

## Case Study 2: Honan's "Epic Hacking" (Social Engineering Chain)

**No technical hacking.** Each step used public/obtainable info:
1. Amazon: partial credit card → added new card
2. Apple: added card + billing address → reset Apple ID
3. Apple ID → **remotely wiped** all Apple devices
4. Gmail + Twitter → reset via daisy-chained account recovery

**Lesson:** Account recovery mechanisms are weak links; chained social engineering can cascade.

---

## Case Study 3: Phishing

| Type | Definition |
|---|---|
| **Phishing** | Trick user into fraudulent version of legitimate site |
| **Spear phishing** | Targeted phishing at specific user/group |

- **80%** of reported incidents are phishing-related
- **$17,700 loss per minute**

### Why Phishing Works
- Users have incomplete mental models of HTTPS
- Can't distinguish browser chrome vs. website content
- Security indicators rarely clicked

### Defenses
- Spam filtering, domain blacklisting
- User training (keep material up-to-date — indicators change)
- **Passkeys / FIDO2** — phishing-resistant authentication

### URL Spoofing
- `<a href="evil.com">good.com</a>` — displayed ≠ actual link
- **Typosquatting:** `paypaI.com` (capital I), `pay.pal.com`, `paypal-security.com`

---

## Key Terminology

| Term | Definition |
|---|---|
| **Social engineering** | Tricking a user into taking some action |
| **Identity theft** | Taking over a victim's real-world identity |
| **Mental model** | User's view of how a system works; directs their actions |
| **Typosquatting** | Domains that are typos/lookalikes of legitimate ones |

---

## Standards Summary

- Standards concentrate elementary knowledge and enable comparison/evaluation
- Plenty exist — **choose the one that fits** rather than re-inventing the wheel
- **AI-generated code = external PR** — always review critically

---

## Related

- [[../concepts/Risk-Assessment|Risk Assessment]] — security policy cycle, audit
- [[../concepts/Authentication|Authentication]] — phishing vs. passkeys
- [[L09-SecurityPolicy-Risk|L09 – Security Policy & Risk]] — risk assessment cycle
- [[L11-UsableSecurity|L11 – Usable Security]] — mental models, phishing UX
- [[L12-SecureProgramming|L12 – Secure Programming]]
