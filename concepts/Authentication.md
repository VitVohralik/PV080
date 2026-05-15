---
tags: [pv080, concept, authentication, iam]
---

# Authentication

> Related lectures: [[../lectures/L03-PKI-eIDAS-Protocols|L03]] | [[../lectures/L04-TLS-SSH-PostQuantum|L04]] | [[../lectures/L05-IAM-Biometrics|L05]]

## Definition

**Authentication** = assurance that a principal, data, or software is genuine.  
**Entity authentication** = assurance that the identity of a principal is as claimed → supports authorization.  
**Data origin authentication** = assurance that the data source is genuine → implies data integrity.

Can sometimes be performed **by a human alone** (e.g., face recognition) or by a **computer alone** (e.g., certificate check).

## Authentication Factors

| Factor | Examples |
|--------|---------|
| **Something we know** | Passwords, PINs, passphrases |
| **Something we have** | Hardware token, smart card, cell phone |
| **Something we are** | Biometrics (fingerprint, face, iris) |
| **Somewhere we are** | Geolocation (GPS), proximity |

**Multi-Factor Authentication (MFA):** combination from **at least two** different categories.  
**SMS is NOT considered strong authentication** (SIM swapping, SS7 attacks) — NIST SP 800-63B deprecated it.

## Challenge-Response Protocols

### Three Principal Levels

1. **Weak secret (password)** — susceptible to brute force, dictionary, replay
2. **Challenge-response with cryptographic key** — most common
3. **Zero-knowledge proof** — best; proves knowledge without revealing the secret

### Challenge-Response with Symmetric Crypto

```
1. A ← B: r_B          (B sends random challenge)
2. A → B: E_K(r_B, "B") (A encrypts challenge with shared key K)
```

Vulnerabilities: reflection attack, relay attack. Cannot provide non-repudiation.

### Challenge-Response with Asymmetric Crypto (via signing)

```
1. A ← B: r_B
2. A → B: r_A, "B", Sign_A(r_A, r_B, "B")    (B needs trusted cert_A)
```

## Time Variant Parameters (TVPs)

| TVP | Properties | Use case |
|-----|-----------|---------|
| **Nonce (random number)** | Unpredictable, never re-used | Freshness, replay prevention |
| **Timestamp** | Timeliness; requires synchronized clocks | Time-bounded protocols |
| **Sequence number** | Uniqueness (not unpredictability) | Message ordering |

## Kerberos

Protocol suite for mutual authentication over insecure networks (Windows AD, corporate).  
Based on **symmetric-key crypto** + trusted third party (KDC).

| Component | Role |
|-----------|------|
| **AS** (Authentication Server) | Authenticates client identity |
| **KDC** (Key Distribution Center) | Issues session keys and tickets |
| **TGS** (Ticket-Granting Service) | Issues service tickets based on TGT |

```
Definitions: L = lifetime; ticket_B = E_K_BT(k, "A", L); auth = E_k("A", T_A)

1. A → T: "A", "B", n_A
2. A ← T: ticket_B, E_K_AT(k, n_A, L, "B")
3. A → B: ticket_B, auth
4. A ← B: E_k(T_A)
```

Timestamps prevent replay (clocks must be synchronized, ~5 min tolerance).

## Needham-Schroeder Symmetric Key Protocol

```
1. A → S: A, B, N_A
2. S → A: { N_A, K_AB, B, { K_AB, A }_K_BS }_K_AS
3. A → B: { K_AB, A }_K_BS
4. B → A: { N_B }_K_AB
5. A → B: { N_B - 1 }_K_AB
```

**Vulnerability (Denning-Saco 1981):** attacker replaying old K_AB; fix: add timestamp (Kerberos) or nonce from B.

## Needham-Schroeder Public-Key + Lowe's Attack

Original protocol vulnerable to MITM (Lowe 1996).  
**Fix:** add responder's identity B into message 6: `{ N_A, N_B, B }_K_PA`

**Lesson:** Be explicit — include all relevant identities in encrypted messages.

## Protocol Attacks

| Attack | Description |
|--------|-------------|
| **Replay** | Re-use previously captured message |
| **Reflection** | Replay message back to originating party |
| **Relay** | Forward message in real time |
| **Interleaving** | Weave messages from concurrent protocol runs |
| **MITM** | Proxy between end-parties |
| **Dictionary** | Heuristically prioritized guessing |

## Password Storage

Passwords stored **salted and hashed** — NOT encrypted.  
- Hash is one-way — compromise reveals only hashes, requiring cracking per-password.  
- Password managers need plaintext retrieval → encryption is appropriate there.

## SSO & OAuth

- **SSO:** one authentication credential for multiple services; reduces exposure
- **SAML 2.0:** XML-based SSO standard (SP → IdP → assertions)
- **OAuth 2.0:** delegated authorization protocol (access tokens, not credentials)

## Related

- [[Biometrics|Biometrics]]
- [[PKI-Certificates|PKI & Certificates]]
- [[TLS-SSL|TLS/SSL]]
- [[../lectures/L03-PKI-eIDAS-Protocols|L03 – PKI, eIDAS, Protocols]]
- [[../lectures/L04-TLS-SSH-PostQuantum|L04 – TLS, SSH, Post-Quantum]]
- [[../lectures/L05-IAM-Biometrics|L05 – IAM & Biometrics]]
