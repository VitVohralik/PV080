---
tags: [pv080, concept, cryptography, hash]
---

# Hash Functions

> Related lectures: [[../lectures/L02-Hash-MAC-PublicKey|L02]]

## Definition

A hash function H maps an **arbitrary-length input** to a **fixed-length digest** (hash value).  
Hash functions are **unkeyed** — they provide integrity but NOT authentication.

## Required Security Properties

| Property | Definition | Attack complexity |
|----------|-----------|------------------|
| **One-wayness** (Preimage resistance) | Given h, infeasible to find m s.t. H(m) = h | O(2^s) |
| **2nd Preimage resistance** (Weak collision resistance) | Given m, infeasible to find m' ≠ m with H(m) = H(m') | O(2^s) |
| **Collision resistance** (Strong) | Infeasible to find any m ≠ m' with H(m) = H(m') | O(2^(s/2)) — **birthday paradox!** |

### Birthday Paradox

In a group of ~23 people, probability of shared birthday > 50%.  
For hash with s-bit output: collisions expected after ~**2^(s/2)** evaluations.  
**Implication:** hash output must be ≥ 2× the desired security level.

## Standards

| Function | Output size | Status |
|----------|-------------|--------|
| **SHA-3** (Keccak) | 224 / 256 / 384 / 512 bits | ✅ Current standard (2015) |
| **SHA-2** (SHA-256, SHA-512) | 256 / 512 bits | ✅ Widely used |
| **SHA-1** | 160 bits | ❌ Deprecated 2017 — SHAttered attack |
| **MD5** | 128 bits | ❌ Broken |

**SHAttered (2017):** First real SHA-1 collision — two PDFs with same SHA-1 hash.

## MAC (Message Authentication Code)

A **keyed** hash — uses a secret key to produce an authentication tag.  
Provides **data integrity + data authentication** (unlike plain hash).

### HMAC (RFC 2104)

```
HMAC(K, m) = H( (K ⊕ opad) ∥ H( (K ⊕ ipad) ∥ m ) )
```

- **ipad** = 0x36 repeated; **opad** = 0x5C repeated
- Resistant to length-extension attacks
- Current standard for message authentication

### Hash vs MAC vs Digital Signature

| | Hash | MAC | Digital Signature |
|--|------|-----|-----------------|
| Key | None | Symmetric | Asymmetric |
| Authentication | ❌ | ✅ | ✅ |
| Non-repudiation | ❌ | ❌ | ✅ |

## Authenticated Encryption Approaches

| Approach | Security |
|----------|---------|
| **Encrypt-then-MAC** | **Best** — verify integrity before decryption |
| **MAC-then-Encrypt** | Vulnerable to padding oracle (used in old TLS) |
| **Encrypt-and-MAC** | MAC may leak PT info (used in SSH) |

## Related

- [[Digital-Signatures|Digital Signatures]]
- [[AES-DES|AES & DES]]
- [[../lectures/L02-Hash-MAC-PublicKey|L02 – Hash, MAC, Public Key]]
