---
tags: [pv080, concept, cryptography, pki, certificates]
---

# PKI & Certificates

> Related lectures: [[../lectures/L03-PKI-eIDAS-Protocols|L03]] | [[../lectures/L04-TLS-SSH-PostQuantum|L04]]

## Public-Key Certificate

A **public-key certificate** binds a public key to an identity, signed by a trusted **Certification Authority (CA)**.  
Can be used even when the CA is **offline** — it's a signed document.

## X.509v3 Certificate Fields

| Field | Contents |
|-------|---------|
| `Version` | X.509v3 |
| `Serial-Number` | Uniquely identifies certificate (used for revocation) |
| `Issuer` | Issuing CA's name |
| `Validity-Period` | Not-Before, Not-After dates |
| `Subject` | Owner's name |
| `Public-Key info` | Algorithm + key value |
| `Digital-Signature` | Signature of Issuer (CA) |

### Critical vs. Non-Critical Extensions

- **Non-critical:** if unknown, system can ignore and continue
- **Critical:** if unknown, system **must reject** the certificate

Common extensions: `Key-Usage`, `Extended-Key-Usage`, `Subject-Alternate-Name`, `Name-Constraints`

## Certificate Chain Validation

```
[Self-signed Root CA (trust anchor)]
        → signs → [Intermediate CA cert]
                        → signs → [Target cert]
```

All signatures must validate for the chain to be trusted.

### Trust Approaches

- **TOFU** (Trust On First Use) — liberal; trust unless blacklisted
- **COFU** (Check On First Use) — conservative; verify on first encounter

## Certificate Revocation

### CRL (Certificate Revocation List)
- Published by CA — a **blacklist** of serial numbers
- Updated periodically → may be stale

### OCSP (Online Certificate Status Protocol)
- Real-time query: "is this certificate still valid?"
- Response: **good**, **revoked**, or **unknown**
- **Preferred** — provides freshness

## PKI Components

| Component | Role |
|-----------|------|
| **CA** (Certification Authority) | Issues and signs certificates |
| **RA** (Registration Authority) | Handles identity verification for CA |
| **Certificate Directory** | Public certificate storage |
| **Cert Status Info** | CRL / OCSP services |

## PKI Trust Models

| Model | Description |
|-------|-------------|
| **Hierarchical** | Root CA → Intermediate CA → End-entity; single path |
| **Browser model** | Multiple disjoint trees; root CAs embedded in OS/browser |
| **Ring-mesh** | Each CA signs the other's cert → O(n²) |
| **Bridge-CA (Hub-and-Spoke)** | All CAs cross-certify with central hub → O(n) |

## CA Key Compromise

- User key compromised: revoke immediately, generate new key pair
- **CA private key compromised:** all issued certificates are suspect — catastrophic
  - CA root keys stored in **safes and bunkers**, encrypted at rest
  - Recovery: **threshold cryptography** (t-of-n scheme)

## Common Criteria (CC) — Certificate Trust Check

- For **conservative certificate check (COFU):** verify certificate validity on first use; reject if not verified
- For **liberal certificate check (TOFU):** accept unless on a blacklist

## Related

- [[Digital-Signatures|Digital Signatures]]
- [[TLS-SSL|TLS/SSL]]
- [[Authentication|Authentication]]
- [[../lectures/L03-PKI-eIDAS-Protocols|L03 – PKI, eIDAS, Protocols]]
- [[../lectures/L04-TLS-SSH-PostQuantum|L04 – TLS, SSH, Post-Quantum]]
