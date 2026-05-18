---
tags: [pv080, lecture, cryptography, hash, mac, public-key]
lecture: 2
---

# L02 – Hash, MAC & Public-Key Crypto

> Full notes: [[Fulltext|Fulltext]]

## Block Cipher Modes of Operation

| Mode | Parallelizable? | Security | Notes |
|------|----------------|---------|-------|
| **ECB** | Yes | **Insecure** | Same PT → same CT; patterns visible |
| **CBC** | Decrypt only | Secure | IV must be random; sequential encryption |
| **CTR** | Yes | Secure | Turns block cipher into stream cipher; random access |

**Never use ECB** — the "ECB penguin" illustrates pattern leakage.

## Security Goals

| Goal | Provides |
|------|---------|
| **Confidentiality** | Hide data semantics |
| **Data Integrity** | Detect unauthorized modification |
| **Data Authentication** | Verify data origin |
| **Entity Authentication** | Verify identity of communicating party |
| **Non-repudiation** | Sender cannot deny sending |

> Encryption alone provides **only confidentiality**.

## Hash Functions

See [[../concepts/Hash-Functions|Hash Functions]] for full details.

### Required Properties

| Property | Attack complexity |
|----------|------------------|
| **One-wayness** (Preimage resistance) | O(2^s) |
| **2nd Preimage resistance** | O(2^s) |
| **Collision resistance** | O(2^(s/2)) — birthday paradox! |

**Birthday paradox:** collisions after ~2^(s/2) evaluations → output must be ≥ 2× security bits.

| Hash | Status |
|------|--------|
| SHA-3 | ✅ Current standard |
| SHA-256/512 | ✅ Widely used |
| SHA-1 | ❌ Deprecated 2017 (SHAttered) |
| MD5 | ❌ Broken |

**Hash functions are UNKEYED** — integrity only, NOT authentication.

## MAC (Message Authentication Code)

**Keyed hash** — provides **integrity + authentication**.

### HMAC (RFC 2104)

```
HMAC(K, m) = H( (K ⊕ opad) ∥ H( (K ⊕ ipad) ∥ m ) )
```

ipad = 0x36 repeated; opad = 0x5C repeated. Resistant to length-extension attacks.

## Authenticated Encryption

| Approach | Security |
|----------|---------|
| **Encrypt-then-MAC** | **Best** — verify integrity before decryption |
| **MAC-then-Encrypt** | Vulnerable to padding oracle |
| **Encrypt-and-MAC** | MAC may leak PT info |

**AEAD (Authenticated Encryption with Associated Data):** encrypts message + authenticates headers.  
Examples: **AES-GCM**, **ChaCha20-Poly1305** — used in TLS 1.3, SSH, QUIC.

## Public-Key Cryptography

Each party has: **Public key** (distributed freely) + **Private key** (kept secret).  
**Critical:** public key authenticity must be verified — MITM can substitute keys.

### RSA

- Based on **difficulty of factoring large integers**
- **Encryption:** C = M^e mod n; **Decryption:** M = C^d mod n
- **Signing:** S = M^d mod n (private key); **Verification:** M = S^e mod n (public key)
- Minimum: 2048 bits; recommended: 3072+ bits

### Hybrid Encryption

```
1. Generate random symmetric key K_s
2. Encrypt data with K_s (fast — AES)
3. Encrypt K_s with recipient's public key (RSA)
4. Send: [Encrypted K_s] ∥ [Encrypted data]
```

Used in TLS, PGP, S/MIME.

**In PGP:** data encrypted with random symmetric key; symmetric key encrypted with recipient's public key.

## Digital Signatures

See [[../concepts/Digital-Signatures|Digital Signatures]] for full details.

**Provides:** integrity + data authentication + non-repudiation  
**NOT:** confidentiality

| | MAC | Digital Signature |
|--|-----|-----------------|
| Non-repudiation | ❌ | ✅ |
| Key type | Symmetric | Asymmetric |

## Key Size Equivalents

| Symmetric | RSA / DH | ECC |
|-----------|----------|-----|
| 80 bits | 1024 bits | 160 bits |
| 128 bits | 3072 bits | **256 bits** |

ECC achieves same security with much shorter keys.

## Key Points

- **ECB mode is insecure** — never use
- **Hash provides integrity, NOT authentication** — use MAC for authentication
- **SHA-1 and MD5 are broken** — use SHA-256 or SHA-3
- **HMAC is the standard MAC** — double-hashing with ipad/opad
- **Encrypt-then-MAC is the safest AE approach**
- **Digital signatures provide non-repudiation** — MAC does not
- **Hybrid encryption** — asymmetric for key exchange, symmetric for data

## Related

- [[../concepts/Hash-Functions|Hash Functions]]
- [[../concepts/Digital-Signatures|Digital Signatures]]
- [[../concepts/AES-DES|AES & DES]]
- [[L01-Symmetric-Encryption|L01 – Symmetric Encryption]]
- [[L03-PKI-eIDAS-Protocols|L03 – PKI, eIDAS, Protocols]]
