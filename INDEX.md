---
tags: [pv080, index, navigation]
---

# PV080 – Information Security & Cryptography — Index

> Full original notes: [[fulltext|fulltext.md]]

---

## Lectures

| # | Lecture | Key Topics |
|---|---------|-----------|
| L01 | [[lectures/L01-Symmetric-Encryption\|Symmetric Encryption]] | DES, AES, stream/block ciphers, OTP, Kerckhoffs, PRNG |
| L02 | [[lectures/L02-Hash-MAC-PublicKey\|Hash, MAC & Public-Key Crypto]] | Hash functions, birthday paradox, HMAC, RSA, digital signatures, hybrid encryption |
| L03 | [[lectures/L03-PKI-eIDAS-Protocols\|PKI, eIDAS & Protocols]] | X.509, CRL/OCSP, PKI trust models, eIDAS, challenge-response, DH |
| L04 | [[lectures/L04-TLS-SSH-PostQuantum\|TLS, SSH & Post-Quantum]] | STS, Needham-Schroeder, Kerberos, TLS 1.3, SSH, quantum, ROCA |
| L05 | [[lectures/L05-IAM-Biometrics\|IAM & Biometrics]] | Authentication factors, biometrics (FAR/FRR/FTE/EER), passwords, RBAC, SAML, OAuth |
| L06 | [[lectures/L06-Privacy\|Privacy]] | Anonymity, pseudonymity, unlinkability, unobservability, mix networks, TOR, CC privacy |
| L07 | [[lectures/L07-GDPR-Ethics\|GDPR & Ethics]] | Personal data, controller/processor, processing principles, data subject rights, inference, statistical databases, ethics |
| L08 | [[lectures/L08-ComputerSecurity-Intro\|Computer Security Intro]] | Security goals (C/I/A+), trusted vs. trustworthy, taxonomy, attacker model, STRIDE |
| L09 | [[lectures/L09-SecurityPolicy-Risk\|Security Policy & Risk]] | Risk formula, qualitative/quantitative analysis, ALE, Bell-LaPadula, ISMS, BCP, RPO/RTO |
| L10 | [[lectures/L10-NetworkAttacks-IDS\|Network Attacks & IDS]] | IDS/IPS, detection methods, DDoS, ARP spoofing, vulnerability assessment, CVE |
| L11 | [[lectures/L11-UsableSecurity\|Usable Security]] | Usability dimensions, 5 principles, habituation, password mental models, secure defaults |
| L12 | [[lectures/L12-SecureProgramming\|Secure Programming]] | SDL, fuzzing, SAST/DAST, DEP/ASLR/CFI/stack canary, taint analysis, crypto issues |
| L13 | [[lectures/L13-Standards-CaseStudies\|Standards, Case Studies & Closing]] | ISO/NIST/FIPS, Common Criteria (TOE/PP/ST/EAL), FIPS 140, phishing, social engineering |

---

## Concept Reference Cards

| Concept | Description |
|---------|-------------|
| [[concepts/AES-DES\|AES & DES]] | Block ciphers, AES rounds/operations, DES history, 3DES, cipher modes |
| [[concepts/Hash-Functions\|Hash Functions]] | One-wayness, 2nd preimage resistance, collision resistance, birthday paradox, HMAC |
| [[concepts/Digital-Signatures\|Digital Signatures]] | What signatures provide (NOT confidentiality), signing process, algorithms, eIDAS types |
| [[concepts/PKI-Certificates\|PKI & Certificates]] | X.509v3 fields, critical extensions, chain validation, CRL/OCSP, trust models |
| [[concepts/TLS-SSL\|TLS/SSL]] | TLS 1.3 handshake, record layer, key exchange, unilateral auth, myths, attacks |
| [[concepts/Authentication\|Authentication]] | Challenge-response, Kerberos, Needham-Schroeder, protocol attacks, TVPs, SSO |
| [[concepts/Biometrics\|Biometrics]] | FAR, FRR, FTE, EER, verification vs. identification, sensor types, liveness detection |
| [[concepts/Privacy-Anonymity\|Privacy & Anonymity]] | 4 privacy functionalities (Pfitzmann & Hansen), pseudonym hierarchy, TOR, onion routing |
| [[concepts/GDPR\|GDPR]] | Personal data, controller/processor, processing principles, sensitive data, data subject rights |
| [[concepts/Risk-Assessment\|Risk Assessment]] | Risk = T·V·C, ALE, qualitative matrix, Bell-LaPadula, ISMS, RPO/RTO |
| [[concepts/IDS-IPS\|IDS/IPS]] | Signature/specification/anomaly-based, TP/FP/FN/TN, DDoS, ARP spoofing |

---

## Quick Exam Reference

### Cryptography

- **Encryption provides:** confidentiality ONLY (not integrity, not authentication)
- **Hash provides:** integrity ONLY — no authentication (unkeyed)
- **MAC provides:** integrity + authentication (keyed, symmetric)
- **Digital signature provides:** integrity + authentication + **non-repudiation** — NOT confidentiality
- **Hybrid encryption:** asymmetric for key exchange, symmetric for data (PGP, TLS, S/MIME)
- **AES rounds** determined by **key length** (not block size): 10/12/14 for 128/192/256 bits
- **ECB is insecure** — same PT block → same CT block; never use
- **IV must be unique** (not necessarily secret); reuse breaks stream cipher security
- **Kerckhoffs:** security comes from key secrecy, not algorithm secrecy

### Hash Functions

- **Birthday paradox:** collisions after ~2^(s/2) evaluations → output ≥ 2× security bits
- **SHA-1, MD5 are broken** → use SHA-256 or SHA-3
- **HMAC:** `H((K⊕opad) ∥ H((K⊕ipad) ∥ m))` — double hash, resistant to length-extension

### Public Key & PKI

- **RSA:** based on factoring; encrypt with public key, sign with private key
- **DH:** key agreement based on discrete log; unauthenticated DH → MITM vulnerability
- **X.509 critical extension:** if unknown → reject certificate (not just ignore)
- **OCSP preferred over CRL** — provides fresh revocation status
- **QES = legally equivalent to handwritten signature** in EU; cross-border recognized

### TLS

- **TLS authentication is unilateral by default** (server only)
- **TLS does NOT provide non-repudiation** (symmetric MAC in record layer)
- **TLS does NOT use digital signatures for application data**
- **TLS 1.3 key exchange:** DHE (forward secrecy) or PSK or PSK+DHE

### Authentication

- **4 factors:** know / have / are / somewhere we are
- **MFA:** at least 2 different factor categories
- **SMS is NOT strong 2FA** — SIM swapping, SS7 (NIST SP 800-63B deprecated)
- **Challenge-response:** prevents replay attacks; use nonces for freshness
- **Needham-Schroeder symmetric:** replay attack → fix with timestamp (Kerberos) or nonce
- **NS public-key Lowe's attack:** fix by adding responder identity B into message 6
- **Kerberos:** tickets + timestamps; NRU of KDC after getting ticket

### Biometrics

- **FAR:** impostor accepted (threshold too lenient) — high → less secure
- **FRR:** legitimate user rejected (threshold too strict) — high → less usable
- **FTE:** failure to enroll
- **EER:** point where FAR = FRR — used for comparing systems
- **Verification = 1:1; Identification = 1:N**
- **If unauthorized users = "greater evil"** → lower FAR → raise threshold → more FRR

### Privacy (Pfitzmann & Hansen)

- **Anonymity:** identity not revealed
- **Pseudonymity:** identity hidden but accountability possible
- **Unlinkability:** multiple uses cannot be linked
- **Unobservability:** even usage of service is hidden
- **Unobservability ⇒ anonymity** (w.r.t. same attacker)
- **Transaction pseudonym** = most anonymous; person pseudonym = most linkable

### Privacy CC Components

- **FPR_ANO** — anonymity; **FPR_PSE** — pseudonymity; **FPR_UNL** — unlinkability; **FPR_UNO** — unobservability
- CC approach: existential (Y/N), not quantitative

### TOR / Onion Routing

- TOR additions vs. basic OR: **directory servers, hidden services, integrity checking** (defends against tagging attacks)
- Tagging attack: modifying ciphertext to track it through the network; integrity checking defeats this
- Attack on TOR: if attacker controls both entry + exit nodes, timing correlation reveals users

### Risk Analysis

- **Quantitative (ALE = Σ Fᵢ · Cᵢ):** output in numbers/money; hard to automate for rare events; requires large historical data
- **Qualitative:** output in risk levels (low/medium/high); partially automatable; more practical
- **Risk = T · V · C = P · C**
- **Bell-LaPadula: NRU (no read up) + NWD (no write down)**

### GDPR

- **Controller** initiates processing; **processor** executes it
- **Sensitive data:** racial/ethnic origin, political opinions, religious beliefs, trade union, genetic/biometric, health, sexual orientation, criminal records
- **6 legal bases:** contract, legal obligation, legitimate interests, public interest, vital interests, consent
- **Art. 5 principles:** lawful/fair/transparent, purpose limitation, minimisation, accuracy, storage limitation, security + accountability
- **Positive compromise:** query series yields exactly 1 individual in statistical database
- **Perturbation:** adding noise to statistical database results to prevent inference

### Common Criteria (CC)

- **TOE** = target of evaluation; **TSF** = TOE security functions; **TSC** = TSF scope of control
- **PP** = Protection Profile (catalogued, reusable); **ST** = Security Target (claimed security)
- **EAL1–EAL7:** evaluation assurance levels (7 = highest)
- **FIPS 140:** cryptographic module validation; MD5 NOT approved

### Computer Security Goals

- **Confidentiality:** data accessible only to authorized parties
- **Integrity:** data unaltered except by authorized parties
- **Authorization:** computing resources accessible only by authorized entities
- **Availability:** protection from DoS
- **Authentication:** assurance that principal/data is genuine
- **Accountability:** ability to identify principals responsible for past actions

### IDS

- **Signature-based:** known attacks only; fast; few FPs
- **Specification-based:** new attacks; expert-defined legitimate specs
- **Anomaly-based:** new attacks; learning-based; more FPs; needs training period
- **Host-based:** from logs; **Network-based:** from packets
- **FN (missed intrusion) = most dangerous** outcome

### Secure Programming

- **5 layers:** source code, testing, compiler, execution environment, defence in depth
- **DEP:** non-executable memory pages; **ASLR:** randomize addresses; **CFI:** restrict valid jumps; **Stack canary:** random value before return address
- **Fuzzing:** random inputs to find crashes; easy to start; finds low-hanging fruit
- **Static analysis:** higher FPs, works on unfinished code; **Dynamic:** lower FPs, needs coverage
- **Never implement your own crypto**
