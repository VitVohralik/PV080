---
tags: [pv080, lecture, pki, certificates, eidas, protocols]
lecture: 3
---

# L03 – PKI, eIDAS & Cryptographic Protocols

> Full notes: [[../fulltext|fulltext]]

## Digital Signatures — Recap

**Provides:** data integrity + data origin authentication + **non-repudiation**  
**MAC provides:** integrity + authentication — **NO non-repudiation**

Actual signing = signing the **hash** of data (asymmetric crypto is slow for full data).

**4 key steps:** key generation → public-key certification → signature generation → signature verification

## Public Key Certificates (X.509v3)

See [[../concepts/PKI-Certificates|PKI & Certificates]] for full details.

Key fields: `Version`, `Serial-Number`, `Issuer`, `Validity-Period`, `Subject`, `Public-Key info`, `Digital-Signature`

**Critical extension:** if unknown, system **must reject** the certificate.  
**Non-critical extension:** if unknown, system can ignore and continue.

## Certificate Chain Validation

```
[Self-signed Root CA] → signs → [Intermediate CA] → signs → [Target cert]
```

All signatures must validate; client starts with trusted root CA from OS/browser.

## Certificate Revocation

| Method | Description | Freshness |
|--------|-------------|---------|
| **CRL** | Blacklist of serial numbers; periodic updates | May be stale |
| **OCSP** | Real-time query; response: good/revoked/unknown | **Preferred** |

## Public-Key Infrastructure (PKI)

| Component | Role |
|-----------|------|
| **CA** | Issues and signs certificates |
| **RA** | Handles identity verification for CA |
| **Certificate Directory** | Public certificate storage |
| **Cert Status Info** | CRL / OCSP services |

### PKI Trust Models

| Model | Scale |
|-------|-------|
| **Hierarchical** | Simple; single path to root |
| **Browser model** | Multiple disjoint root CA trees in OS/browser |
| **Ring-mesh** | O(n²) |
| **Bridge-CA (Hub-and-Spoke)** | O(n) |

## eIDAS Regulation (EU 910/2014)

Covers electronic identification (eID) and trust services for electronic transactions in EU.  
Updated by: eIDAS 2.0 — Regulation (EU) 2024/1183

### eIDAS Assurance Levels (Art. 8)

| Level | Requirements |
|-------|-------------|
| **Low** | At least 1 authentication factor |
| **Substantial** | At least **2 factors** from different categories |
| **High** | Substantial + protection against duplication/tampering |

### Electronic Signature Types

| Type | Legal strength |
|------|---------------|
| **(Simple) Electronic Signature** | Lowest — any electronic signing data |
| **Advanced Electronic Signature** | Certificate-based, no quality requirement |
| **Qualified Electronic Signature (QES)** | Qualified cert + qualified device; **= handwritten signature** legally (Art. 25) |

QES cross-border recognized in all EU member states.

**eIDAS 2.0 new features:** European Digital Identity Wallet (EUIDW), qualified archivation.

## Cryptographic Protocols

A **cryptographic protocol** = algorithm specifying message exchange using cryptographic techniques.

| Category | Sub-type |
|----------|---------|
| **Entity authentication** | Unilateral / Mutual |
| **Key establishment** | Key transport / Key agreement |

## Entity Authentication Levels

1. **Weak secret (password)** — susceptible to brute force, dictionary, replay
2. **Challenge-response with crypto key** — most common
3. **Zero-knowledge proof** — best; proves knowledge without revealing secret

**Claimant** = party being authenticated; **Verifier** = party receiving assurances.

## Challenge-Response Protocols

### Symmetric Crypto

```
1. A ← B: r_B
2. A → B: E_K(r_B, "B")
```

Proves A knows shared key K. Vulnerable to reflection attack.

### Asymmetric Crypto (via signing)

```
1. A ← B: r_B
2. A → B: r_A, "B", Sign_A(r_A, r_B, "B")
```

## Time Variant Parameters (TVPs)

| TVP | Properties |
|-----|-----------|
| **Nonce** | Unpredictable, never re-used; prevents replay |
| **Timestamp** | Timeliness; requires synchronized clocks |
| **Sequence number** | Uniqueness only (not unpredictability) |

## Diffie-Hellman Key Agreement

```
Public parameters: prime p, generator g

1. A → B: g^a mod p     (A picks secret a)
2. A ← B: g^b mod p     (B picks secret b)

Both compute: K = g^(ab) mod p
```

Security: hardness of **discrete logarithm problem**.  
**Critical weakness:** unauthenticated DH is vulnerable to **MITM attack**.  
**Fix:** authenticated DH — bind to verified public keys (certificates).

## Protocol Attacks

| Attack | Description |
|--------|-------------|
| **Replay** | Re-use previously captured message |
| **Reflection** | Replay message back to originator |
| **Relay** | Forward message in real time |
| **MITM** | Proxy between end-parties |
| **Dictionary** | Heuristic guessing |

**Defense:** use Time Variant Parameters (nonces, timestamps).

## Key Points

- **Digital signatures = integrity + authentication + non-repudiation**
- **Critical extension:** if unknown → reject certificate
- **OCSP preferred over CRL** — provides fresh revocation status
- **QES = legally equivalent to handwritten signature**, cross-border recognized
- **DH enables key agreement** but vulnerable to MITM without authentication
- **Nonces provide freshness** and prevent replay attacks

## Related

- [[../concepts/Digital-Signatures|Digital Signatures]]
- [[../concepts/PKI-Certificates|PKI & Certificates]]
- [[../concepts/Authentication|Authentication]]
- [[L02-Hash-MAC-PublicKey|L02 – Hash, MAC, Public Key]]
- [[L04-TLS-SSH-PostQuantum|L04 – TLS, SSH, Post-Quantum]]
