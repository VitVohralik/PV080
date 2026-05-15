---
tags: [pv080, lecture, tls, ssh, post-quantum, protocols]
lecture: 4
---

# L04 – TLS, SSH & Post-Quantum Crypto

> Full notes: [[../fulltext|fulltext]]

## Station-to-Station (STS) Protocol

Turns unauthenticated DH into **authenticated DH** using digital signatures.

```
(1) A → B: g^a
(2) A ← B: g^b,  { S_B(g^b, g^a), cert_B }_K
(3) A → B:       { S_A(g^a, g^b), cert_A }_K
```

K = g^(ab) mod p — derived via DH (not pre-shared).  
Provides: **mutual authentication + forward secrecy**.

## Key Security Properties

| Property | Definition |
|----------|-----------|
| **Forward secrecy** | Long-term key compromise does NOT compromise past session keys |
| **Ephemeral key** | Destroyed at session end — cannot be recovered |
| **Known-key security** | Compromise of one session key doesn't endanger future sessions |

**Implicit key auth:** only intended party could have computed K.  
**Explicit key auth:** implicit + key-use confirmation.

## Needham-Schroeder Protocols

### Symmetric Key Protocol (1978)

```
1. A → S: A, B, N_A
2. S → A: { N_A, K_AB, B, { K_AB, A }_K_BS }_K_AS
3. A → B: { K_AB, A }_K_BS
4. B → A: { N_B }_K_AB
5. A → B: { N_B - 1 }_K_AB
```

**Denning-Saco replay attack (1981):** attacker replays step 3 with old K_AB; B cannot detect.  
**Fix:** add timestamp (Kerberos) or nonce from B in ticket.

### Public-Key Protocol + Lowe's Attack (1996)

Original protocol vulnerable: Mallory can use Alice as oracle to make Bob think he's talking to Alice.

**Fix (Needham-Schroeder-Lowe):** add B's identity to message 6:

```
6. B → A: { N_A, N_B, B }_K_PA     ← added "B"
```

**Lesson:** Be explicit — include all relevant identities in encrypted messages.

## Kerberos

Mutual authentication over insecure networks. Based on symmetric crypto + trusted third party (KDC).  
Based on Needham-Schroeder symmetric protocol with timestamps.

See [[../concepts/Authentication|Authentication]] for Kerberos protocol details.

## TLS (Transport Layer Security)

See [[../concepts/TLS-SSL|TLS/SSL]] for full details.

**Two layers:**
- **Handshake layer** — key exchange + server authentication
- **Record layer** — protects application data (AEAD)

**What TLS provides:**
- Confidentiality (encryption)
- Integrity (AEAD)
- **Entity authentication: unilateral by default** (server only)

**TLS 1.3 key exchange options:**
- **DHE/ECDHE** — provides forward secrecy
- **PSK** — no forward secrecy alone
- **PSK + DHE** — forward secrecy with PSK optimization

**TLS Common Myths:**
1. TLS provides non-repudiation — **FALSE** (symmetric MAC)
2. TLS uses digital signatures for application data — **FALSE** (only handshake auth and certs)

**TLS Attacks:** Heartbleed (impl.), Logjam/DROWN (downgrade), Lucky Thirteen (padding oracle).

## SSH (Secure Shell)

Encrypted tunnel over unsecured network. Three protocol layers:

1. **Transport layer** — server authentication (TOFU or CA), crypto parameter negotiation
2. **User authentication protocol** — password / Kerberos ticket / **client public key** (most secure)
3. **Connection protocol** — multiplexes into multiple channels (shell, SFTP, port forwarding)

**TOFU (Trust On First Use):** client trusts server key on first connection; stores fingerprint locally.

## Cryptography in the Quantum Era

### Threats from Quantum Computers

| Problem | Used in | Quantum threat |
|---------|---------|---------------|
| **Integer factorization** | RSA, Rabin | Broken by **Shor's algorithm** |
| **Discrete logarithm** | DSA, ECDSA, DH | Broken by **Shor's algorithm** |

**Symmetric ciphers:** Grover's algorithm halves effective key length → **double key length** (AES-256 still secure).  
**Hash functions:** Grover reduces collision search → use **longer output** (SHA-256+).

### Post-Quantum Cryptography (PQC) — NIST 2024

| Hard problem | Algorithms |
|-------------|-----------|
| **Shortest vector in lattice** | **Kyber** (CRYSTALS-Kyber — NIST KEM standard), NTRU |
| **Hash-based** | SPHINCS+ |
| **Decoding problem** | McEliece |

**Broken PQC:** SIDH (broken 2022), Rainbow (broken).

**Short-term mitigation:** add symmetric crypto layer (not affected by quantum) on top of existing protocols.

## Case Studies

**Bias in RSA keys:** Different implementations produce statistically distinct fingerprints in (p, q) distribution. Enables library identification, weak key search, auditing.

**ROCA vulnerability (2017):** Infineon Technologies used non-standard prime form → reduced prime pool → factorizable keys. Affected Estonian/Slovak eID cards; 2048-bit keys factorized.  
**Lesson: Follow standards!** Optimization of prime generation must preserve full entropy.

## Key Points

- **STS:** authenticated DH using signatures — mutual authentication + forward secrecy
- **NS symmetric:** replay attack fixed by timestamps (Kerberos) or nonces
- **NS public-key:** Lowe's MITM fixed by including responder identity in message 6
- **Kerberos:** tickets + timestamps; widely used in enterprise
- **TLS authentication is unilateral by default** (server only)
- **Quantum:** Shor breaks RSA/ECC/DH; symmetric safe with doubled key
- **ROCA:** always follow standards for prime generation

## Related

- [[../concepts/TLS-SSL|TLS/SSL]]
- [[../concepts/PKI-Certificates|PKI & Certificates]]
- [[../concepts/Authentication|Authentication]]
- [[L03-PKI-eIDAS-Protocols|L03 – PKI, eIDAS, Protocols]]
- [[L05-IAM-Biometrics|L05 – IAM & Biometrics]]
