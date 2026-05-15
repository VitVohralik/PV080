---
tags: [pv080, lecture, cryptography, symmetric-encryption]
lecture: 1
---

# L01 – Symmetric Encryption

> Full notes: [[../fulltext|fulltext]]

## Core Terminology

| Term | Definition |
|------|-----------|
| **Cryptography** | Art & science of **securing data** against adversaries — NOT just encryption |
| **Steganography** | Art of **concealing data within other data** (e.g., watermarking, images) |
| **Cryptanalysis** | Art & science of revealing weaknesses in cryptography |

**When to use steganography:** when encryption is illegal or would draw suspicion.

## Encoding vs. Encryption

| | Encoding | Encryption |
|-|----------|------------|
| Mapping | **Known** (public) | **Secret** (defined by key K) |
| Domain | Different: X → Y | Same: X → X (permutation!) |
| Example | ASCII table | Caesar cipher, AES |

Encryption E_K is a **permutation**. Encoding is NOT encryption — mapping is publicly known.

## Attack Models

| Attack type | What adversary has |
|-------------|-------------------|
| **Ciphertext-only** | CT only |
| **Known-plaintext** | Some (PT, CT) pairs |
| **Chosen-plaintext** | Can choose PT and see CT |
| **Chosen-ciphertext** | Can submit CT and receive PT |

- **Passive adversary** — observes/records only
- **Active adversary** — injects/alters data

## Vernam Cipher (One-Time Pad)

- **Stream cipher:** c_i = m_i ⊕ k_i
- **Theoretically perfect (unbreakable)** — key as long as plaintext, used once only
- Historical use: Moscow–Washington hotline (1963)
- Impractical: key must be as long as message

### XOR Properties

```
A ⊕ B = B ⊕ A
A ⊕ A = 0
A ⊕ B = C  ⟹  A = C ⊕ B
```

**Reused keystream attack:** CT1 ⊕ CT2 = PT1 ⊕ PT2 — keystream cancels, information leaks!

## Stream Ciphers

- Act as deterministic RNG; initialized by **(K, IV)** — pair must be unique
- Encryption: CT = PT ⊕ keystream
- **IV must be unique** (not necessarily secret) — reuse breaks security

| Cipher | Status |
|--------|--------|
| **ChaCha20** | ✅ Current standard, recommended |
| **RC4** | ❌ Weak, dropped in TLS 1.3 |

## DES & AES

See [[../concepts/AES-DES|AES & DES]] for full details.

| Cipher | Block size | Key size | Status |
|--------|-----------|---------|--------|
| DES | 64 bits | 56 bits | ❌ Broken |
| 3DES | 64 bits | 112 bits (eff.) | ⚠️ Being phased out |
| AES | 128 bits | 128/192/256 bits | ✅ Current standard |

AES rounds: 10/12/14 (determined by **key length**).

## Kerckhoffs' Principle

> "A system's security should NOT rely on secrecy of its design — only the key should be secret."

- a.k.a. "The enemy knows the system" (Shannon)
- If algorithm is secret, must replace ALL hardware when it leaks

## Randomness

| Type | Properties |
|------|-----------|
| **TRNG** | Physical process; non-deterministic, slow |
| **PRNG** | Deterministic, fast; periodic; fully determined by seed |
| **CSPRNG** | Cryptographically secure PRNG |

**Recommended:** ChaCha20, AES-CTR, Yarrow/Fortuna (`/dev/urandom`), Blum Blum Shub  
**NOT recommended:** `rand()`, `Java.util.Random`, Mersenne Twister, Dual_EC_DRBG (suspected backdoor)

## Key Points

- **Do NOT implement your own crypto** — use established libraries
- **IV must be unique** (reuse breaks security); does not need to be secret
- **DES is broken** — 56-bit key too short; use AES
- **PRNG is deterministic** — do not use `rand()` for crypto

## Related

- [[../concepts/AES-DES|AES & DES]]
- [[L02-Hash-MAC-PublicKey|L02 – Hash, MAC, Public Key]]
