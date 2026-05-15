---
tags: [pv080, concept, cryptography, symmetric-encryption]
---

# AES & DES — Block Ciphers

> Related lectures: [[../lectures/L01-Symmetric-Encryption|L01]] | [[../lectures/L02-Hash-MAC-PublicKey|L02]]

## DES (Data Encryption Standard)

- **Algorithm:** DEA (Data Encryption Algorithm), based on IBM's LUCIPHER (H. Feistel)
- US standard 1977 (FIPS PUB 46); key length reduced from 128 to **56 bits** by US government
- **Block size:** 64 bits; **Key size:** 56 bits (64 bits with parity)
- **Weak keys (4):** E_K(x) = x (encryption is identity); **Semi-weak key pairs (6):** E_K1(E_K2(x)) = x

### DES Breaking Timeline

| Year | Achievement |
|------|-------------|
| 1977 | Diffie & Hellman proposed $20M breaking machine |
| 1993 | M. Wiener: $1M machine → 3.5 hours |
| 1997 | 70,000 systems, 96 days |
| 1998 | EFF machine ($200k) — breaks keys in **hours** |

**DES is broken. Do NOT use.**

## Triple DES (3DES)

Process: **Encrypt(K1) → Decrypt(K2) → Encrypt(K3)**, where K3 = K1  
Effective key length = **112 bits**; backward compatible with DES (K1 = K2 = K3); being phased out.

## AES (Advanced Encryption Standard)

- **Block size:** 128 bits (fixed)
- **Key lengths:** 128 / 192 / 256 bits
- **Rounds:** 10 / 12 / 14 (based on key length)
- **Rijndael vs AES:** Rijndael has variable block & key sizes; AES restricts them
- NIST standardized 2001 (FIPS 197); Rijndael (Belgian) selected from 5 finalists

### AES Round Operations

| Operation | Purpose |
|-----------|---------|
| **AddRoundKey** | XOR data with round key |
| **SubBytes** | S-box: 8-bit → 8-bit permutation; **only non-linear operation** |
| **ShiftRows** | Row-wise shift — provides diffusion |
| **MixColumn** | Column mixing — avalanche effect |

Key Scheduling: MasterKey expanded into (10/12/14) RoundKeys.  
Number of rounds determined by **key length** (not block size).

## Block Cipher Modes

| Mode | Parallelizable? | Random access? | Security |
|------|----------------|---------------|---------|
| **ECB** | Yes | Yes | **Insecure** — patterns visible |
| **CBC** | Decrypt only | No | Secure; IV must be random |
| **CTR** | Yes | Yes | Secure; turns block cipher into stream cipher |

**ECB is insecure** — same PT block → same CT block. Never use ECB.

## Key Size Comparison

| Symmetric | RSA / DH | ECC |
|-----------|----------|-----|
| 80 bits | 1024 bits | 160 bits |
| 112 bits | 2048 bits | 224 bits |
| **128 bits** | **3072 bits** | **256 bits** |

## Related

- [[Hash-Functions|Hash Functions]]
- [[TLS-SSL|TLS/SSL]]
- [[../lectures/L01-Symmetric-Encryption|L01 – Symmetric Encryption]]
- [[../lectures/L02-Hash-MAC-PublicKey|L02 – Hash, MAC, Public Key]]
