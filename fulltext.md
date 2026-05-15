# PV080 – Information Security and Cryptography

> Navigation: [[INDEX|INDEX]] | Concepts: [[concepts/AES-DES|AES-DES]] · [[concepts/Hash-Functions|Hash Functions]] · [[concepts/Digital-Signatures|Digital Signatures]] · [[concepts/PKI-Certificates|PKI]] · [[concepts/TLS-SSL|TLS]] · [[concepts/Authentication|Authentication]] · [[concepts/Biometrics|Biometrics]] · [[concepts/Privacy-Anonymity|Privacy]] · [[concepts/GDPR|GDPR]] · [[concepts/Risk-Assessment|Risk]] · [[concepts/IDS-IPS|IDS/IPS]]
> Lectures: [[lectures/L01-Symmetric-Encryption|L01]] · [[lectures/L02-Hash-MAC-PublicKey|L02]] · [[lectures/L03-PKI-eIDAS-Protocols|L03]] · [[lectures/L04-TLS-SSH-PostQuantum|L04]] · [[lectures/L05-IAM-Biometrics|L05]] · [[lectures/L06-Privacy|L06]] · [[lectures/L07-GDPR-Ethics|L07]] · [[lectures/L08-ComputerSecurity-Intro|L08]] · [[lectures/L09-SecurityPolicy-Risk|L09]] · [[lectures/L10-NetworkAttacks-IDS|L10]] · [[lectures/L11-UsableSecurity|L11]] · [[lectures/L12-SecureProgramming|L12]] · [[lectures/L13-Standards-CaseStudies|L13]]

---

# Lecture 1 – Cryptographic Building Blocks: Symmetric Encryption

> Lecture notes: [[lectures/L01-Symmetric-Encryption|L01]] | Related concepts: [[concepts/AES-DES|AES & DES]]

## Core Terminology

| Term | Definition |
|------|-----------|
| **Cryptography** | Art & science of **securing data** against adversaries — NOT just encryption |
| **Steganography** | Art of **concealing data within other data** (e.g., watermarking, images) |
| **Cryptanalysis** | Art & science of **revealing weaknesses** in cryptography |
| **Cryptology** | Cryptography + Cryptanalysis |

**When to use steganography over encryption:**
- When encryption is illegal or would draw suspicion
- When you need security by obscurity (though combining both is better)

---

## Encoding vs. Encryption

| | Encoding | Encryption |
|-|----------|------------|
| Mapping | **Known** (public) | **Secret** (defined by key K) |
| Domain | Different: X → Y | Same: X → X (permutation!) |
| Example | ASCII table | Caesar cipher, AES |
| Reversible? | Yes (decoding) | Yes (decryption with key) |

**Key insight:** Encryption E_K is a **permutation** (one-to-one mapping on the same domain).

**Why encoding is NOT encryption:** The mapping is publicly known (e.g., ASCII table) → anyone can decode.

---

## Encryption & Decryption — Basics

- **Encryption:** transforms plaintext (PT) + key K → ciphertext (CT)
- **Decryption:** transforms ciphertext (CT) + key K' → plaintext (PT)
- **Encryption provides data confidentiality** = hides data semantics

**Symmetric encryption:** K = K' (same key for encryption and decryption)

### Key Size Strategy

| Key size | Usage | Attack risk |
|----------|-------|-------------|
| **Small** (1 bit per data bit) | **Changing** key for each bit | Brute force feasible if reused → must be unique |
| **Large** (≥ 128 bits) | **Fixed** key for data blocks | Must be large enough to prevent exhaustive search |

---

## Classical Ciphers

**Caesar cipher:** E_K(x) = (K + x) mod 26  
- Caesar: K = 3, Augustus: K = 1

**Attacks on classical ciphers:**
1. **Brute force** – try all 26 keys, check if PT is meaningful
2. **Statistical attack** – compute letter frequencies in CT, match to known language frequencies

**Countermeasures:**
1. Key space must be **infeasible to search** → key > 100 bits
2. Cipher design producing **random-looking CT** (no statistical patterns)

---

## Attack Models

### By adversary access:

| Attack type | What adversary has |
|-------------|-------------------|
| **Exhaustive search** (brute force) | Tries all possible keys |
| **Ciphertext-only** | Access to CT only |
| **Known-plaintext** | Access to some (PT, CT) pairs, tries to recover other PT or key |
| **Chosen-plaintext** | Can choose PT and see resulting CT |
| **Chosen-ciphertext** | Can submit CT and receive back PT; deduces key |

### By adversary behavior:

- **Passive adversary** – observes/records, does not alter data (ciphertext-only, known-plaintext)
- **Active adversary** – injects/alters data, starts new interactions (chosen-plaintext, chosen-ciphertext)

> **Attacker model** must clearly specify what the attacker **CAN** and **CANNOT** do.

---

## Vernam Cipher (One-Time Pad)

- **Stream cipher** – encrypts one bit at a time: c_i = m_i ⊕ k_i
- **Theoretically perfect (unbreakable)** cipher
- **Requirement:** key must be as long as plaintext → impractical
- **OTP:** key used for **one use only** → expensive, impractical
- Historical use: Moscow–Washington hotline (1963)

### XOR Properties (critical!):
```
A ⊕ B = B ⊕ A
A ⊕ A = 0
A ⊕ B = C  ⟹  A = C ⊕ B
```

### Reused Keystream Attack:
If same keystream KS is used for two messages:  
**CT1 ⊕ CT2 = PT1 ⊕ PT2** → keystream cancels out → information leaked!

- Keystream must be **unique** — reuse allows extraction of PT XOR PT
- Repeated/periodic keystream (abc abc abc…) causes same vulnerability

---

## Stream Ciphers

- Act as **deterministic RNG** (stateful): generate large random keystream
- Initialized by pair **(K, IV)** — the pair must be unique
- Encryption: CT = PT ⊕ keystream
- Operations: only bitwise (XOR, AND, OR, shift) → **extremely fast**

**Types:**
- **Synchronous:** CT = PT ⊕ keystream
- **Asynchronous:** keystream depends on previous ciphertext

**Examples:**
- ✅ **ChaCha20** – current standard, recommended
- ❌ **RC4** – weak, **dropped in TLS 1.3**

### Initialization Vector (IV)

- **Randomizes CT** for the same K and PT
- Does NOT need to be secret, but **must be unique!**
- Same (IV, K) pair → same keystream → vulnerability (reuse attack)

---

## Block Ciphers

- Operate on **fixed-size blocks** (typically 128b, 256b) — permutations
- Same key applied to each block
- Suitable for **software implementation**, parallel processing

### Padding
- Last block may be incomplete → must be padded
- **Padding length stored in the last byte** to distinguish from data
- **Message is always padded** (even if already full block length!)
- Padding schemes can lead to attacks if misused

### Stream vs. Block Ciphers Comparison

| | Stream cipher | Block cipher |
|-|--------------|--------------|
| Unit processed | bit/byte | block (64–256 bits) |
| Speed | comparable (ChaCha20 very fast in SW) | faster with HW acceleration (AES-NI) |
| Best for | unknown file size, audio/video streams | known file size, file encryption |
| Implementation | HW and SW (ChaCha20 is pure SW) | SW and HW (AES-NI) |
| Parallelizable | no | depends on mode (CTR: yes, CBC encrypt: no) |

---

## DES – Data Encryption Standard

- **Algorithm:** DEA (Data Encryption Algorithm)
- Based on IBM's LUCIPHER (H. Feistel), **modified by US government**
  - Key length reduced from **128 bits → 56 bits**
  - S-box redesign kept classified (suspected NSA backdoor considerations)
- US standard 1977 (FIPS PUB 46), last renewal 1994
- **64-bit block** size, **56-bit key** (64 bits with parity)

**Weak keys (4):** E_K(x) = x (encryption = identity)  
**Semi-weak key pairs (6 pairs):** E_K1(E_K2(x)) = x (two keys cancel each other)

### Breaking DES (2^56 keys):
| Year | Achievement |
|------|-------------|
| 1977 | Diffie & Hellman proposed $20M breaking machine |
| 1993 | M. Wiener design: $1M → 3.5 hours; $100k → 35 hours |
| 1997 | 70,000 systems, 96 days |
| 1998 | EFF machine ($200k) — breaks keys in **hours** |

**⟹ DES is broken and should NOT be used.**

---

## Triple DES (3DES)

Process: **Encrypt(K1) → Decrypt(K2) → Encrypt(K3)**, where K3 = K1

- Three 56-bit keys, but **effective key length = 112 bits** (K3 = K1)
- Backward compatible with DES (if K1 = K2 = K3)
- Slower than AES; being phased out

---

## AES – Advanced Encryption Standard

- **Block size:** 128 bits (fixed)
- **Key lengths:** 128 / 192 / 256 bits
- **Rounds:** 10 / 12 / 14 (based on key length)
- **Rijndael vs AES:** Rijndael has variable block & key sizes; AES restricts them

### History:
- 1997: NIST announcement
- 2000: Rijndael selected as winner (5 finalists)
- 2001: FIPS standard

Rijndael chosen for: adequate security, **ease of implementation, fast in both SW & HW**

### AES Building Blocks (each round):

| Operation | Purpose |
|-----------|---------|
| **AddRoundKey** | XOR data with round key |
| **SubBytes** | S-box: 8-bit → 8-bit permutation, **only non-linear operation** |
| **ShiftRows** | Row-wise shift — provides diffusion |
| **MixColumn** | Column mixing — provides diffusion (avalanche effect) |

**Key Scheduling:** MasterKey expanded into (10/12/14) RoundKeys  
**Decryption:** Reversed order of RoundKeys + inverse operations

---

## Kerckhoffs' Principle

> **A system's security should NOT rely on secrecy of its design!**

- a.k.a. "The enemy knows the system" (Shannon)
- Only the **key** should be secret, not the algorithm
- If you hardcode secrets into hardware, you must replace ALL hardware when the secret leaks

**Keyed vs. unkeyed algorithms:**
- **With keys:** ciphers (symmetric, asymmetric), MAC
- **Without keys (unkeyed):** hash functions, RNG

---

## Randomness in Cryptography

Crypto relies on **hard-to-guess and unique values**: keys, IVs, nonces, challenges

### Types of RNG:

| Type | How | Properties |
|------|-----|-----------|
| **TRNG** (True RNG) | Physical process (radioactive decay, thermal noise) | Non-deterministic, **unpredictable**, aperiodic, **slow** |
| **PRNG** (Pseudo RNG) | Deterministic algorithm | Fast, periodic, fully determined by **seed** |

**Problem with PRNG:** Compromised seed/internal state → all future values predictable!

**System RNGs** (e.g., `/dev/urandom`): PRNG seeded by TRNG → combines speed + unpredictability

### CSPRNG – Cryptographically Secure PRNG:

**✅ Recommended:**
- ChaCha20 (stream cipher based)
- AES in CTR mode (block cipher based)
- Yarrow, Fortuna (`/dev/random`, `/dev/urandom`)
- Blum Blum Shub (number-theoretic)

**❌ NOT recommended:**
- `rand()`, `Java.util.Random` — leak internal state
- Mersenne Twister — predictable from output
- Dual_EC_DRBG — suspected backdoor
- RC4

---

---

# Lecture 2 – Cryptographic Building Blocks: Integrity, Authentication & Public-Key Crypto

> Lecture notes: [[lectures/L02-Hash-MAC-PublicKey|L02]] | Related concepts: [[concepts/Hash-Functions|Hash Functions]] · [[concepts/Digital-Signatures|Digital Signatures]] · [[concepts/AES-DES|AES & DES]]

---

## Block Cipher Modes of Operation

Block ciphers encrypt a single fixed-size block. **Modes of operation** define how to apply them to arbitrary-length messages.

### ECB – Electronic Codebook (INSECURE)

- Each block encrypted **independently** with the same key
- **Same PT block → same CT block** — patterns from plaintext are visible in ciphertext
- **Do NOT use ECB** — the "ECB penguin" example illustrates that structure leaks through

### CBC – Cipher Block Chaining

- Each PT block is **XORed with the previous CT block** before encryption
- First block XORed with **IV** (must be random and unique)
- **Sequential** — cannot be parallelized for encryption (decryption can be parallelized)
- Error propagation: one corrupt CT block corrupts **two** PT blocks on decryption

### CTR – Counter Mode

- Generates a **keystream** by encrypting successive counter values (nonce ∥ counter)
- PT XORed with keystream — **turns block cipher into stream cipher**
- **Parallel encryption and decryption**
- **Random access** — any block can be decrypted independently
- Nonce must be unique per key; counter increments per block

| Mode | Parallelizable? | Random access? | IV required? | Security |
|------|----------------|---------------|--------------|---------|
| ECB | Yes | Yes | No | **Insecure** |
| CBC | Decrypt only | No | Yes (random) | Secure |
| CTR | Yes | Yes | Yes (nonce) | Secure |

---

## Security Goals

| Goal | What it provides |
|------|-----------------|
| **Confidentiality** | Hide data semantics — adversary learns nothing about plaintext |
| **Data Integrity** | Detect unauthorized modification of data |
| **Data Authentication** | Verify data origin (who sent it) |
| **Entity Authentication** | Verify the identity of a communicating party |
| **Non-repudiation** | Sender cannot deny sending the message |

> Encryption alone provides **only confidentiality** — not integrity or authentication.

---

## Hash Functions

A hash function H maps an **arbitrary-length input** to a **fixed-length digest** (hash value).

### Required Properties

| Property | Definition | Attack complexity |
|----------|-----------|------------------|
| **One-wayness** (Preimage resistance) | Given h, infeasible to find m such that H(m) = h | O(2^s) |
| **2nd Preimage resistance** (Weak collision resistance) | Given m, infeasible to find m' ≠ m with H(m) = H(m') | O(2^s) |
| **Collision resistance** (Strong) | Infeasible to find any m ≠ m' with H(m) = H(m') | O(2^(s/2)) — **birthday paradox!** |

**Birthday paradox:** In a group of ~23 people, probability of a shared birthday > 50%. For hash with s-bit output, collisions expected after ~2^(s/2) evaluations. This is why hash outputs must be **at least twice** the desired security level.

### Hash Function Standards

| Function | Standardized | Output size | Status |
|----------|-------------|-------------|--------|
| **SHA-3** (Keccak) | 2015 | 224 / 256 / 384 / 512 bits | ✅ Current standard |
| **SHA-2** (SHA-256, SHA-512) | 2001 | 256 / 512 bits | ✅ Widely used |
| **SHA-1** | 1993 | 160 bits | ❌ Deprecated 2017 |
| **MD5** | 1992 | 128 bits | ❌ Widely deprecated |

**SHAttered (2017):** Google & CWI Amsterdam produced the first real SHA-1 collision — two different PDF files with the same SHA-1 hash. SHA-1 is now fully broken for security purposes.

### Hash functions are UNKEYED — they provide integrity but NOT authentication.

---

## Message Authentication Codes (MAC)

A MAC is a **keyed** hash — uses a secret key to produce an authentication tag. Provides **data integrity + data authentication**.

### CBC-MAC

- Apply CBC encryption to the message; the **last ciphertext block** is the MAC
- Only the final block is sent as the tag; intermediate blocks discarded
- Key must be secret; IV is typically fixed (all zeros)

### HMAC (Hash-based MAC) — RFC 2104

```
HMAC(K, m) = H( (K ⊕ opad) ∥ H( (K ⊕ ipad) ∥ m ) )
```

- **ipad** = 0x36 repeated; **opad** = 0x5C repeated
- Double-hashing with key mixed in via XOR — resistant to length-extension attacks
- Works with any hash function (HMAC-SHA256, HMAC-SHA3, etc.)
- Current standard for message authentication

**MAC vs Hash:**
- Hash alone: anyone can recompute — no authentication
- MAC: requires knowledge of secret key → proves origin

---

## Authenticated Encryption

Encryption provides confidentiality; MAC provides integrity/authentication. **Authenticated Encryption (AE)** combines both.

### Three Approaches

| Approach | How | Used in | Security |
|----------|-----|---------|---------|
| **MAC-then-Encrypt** | Compute MAC over PT, then encrypt (PT ∥ MAC) | TLS (older) | Vulnerable to padding oracle |
| **Encrypt-then-MAC** | Encrypt PT first, then MAC over CT | IPsec | **Highest security** — verify before decrypt |
| **Encrypt-and-MAC** | Compute MAC over PT independently, send CT ∥ MAC | SSH | MAC may leak PT info |

> **Encrypt-then-MAC** is the recommended approach. Requires **two separate keys** (one for encryption, one for MAC).

### AEAD – Authenticated Encryption with Associated Data

- Encrypts the **message** (confidentiality) AND authenticates **associated data** (not encrypted, e.g. headers)
- Associated data is authenticated but sent in cleartext
- Examples: **AES-GCM**, **ChaCha20-Poly1305**
- Used in TLS 1.3, SSH, QUIC

---

## Public-Key Cryptography (Asymmetric Encryption)

Each party has a **key pair**:
- **Public key** — distributed freely, used for encryption
- **Private key** — kept secret, used for decryption

**Key exchange problem solved:** no prior shared secret needed.

**Critical requirement:** public key **authenticity** must be verified — if an attacker substitutes their own public key, messages are compromised (MITM attack).

### RSA

- Based on the **difficulty of factoring large integers**
- Key generation: choose two large primes p, q → n = p × q
- Public exponent **e** (commonly 65537); private exponent **d** (computed from p, q, e)
- **Encryption:** C = M^e mod n
- **Decryption:** M = C^d mod n
- **Signing:** S = M^d mod n (sign with private key)
- **Verification:** M = S^e mod n (verify with public key)

**Current minimum RSA key size: 2048 bits; recommended: 3072+ bits**

### Hybrid Encryption

Asymmetric crypto is slow — used only to **encrypt a symmetric session key**.

```
1. Generate random symmetric key K_s
2. Encrypt data with K_s (fast — AES)
3. Encrypt K_s with recipient's public key (RSA)
4. Send: [Encrypted K_s] ∥ [Encrypted data]
```

Used in TLS, PGP, S/MIME.

---

## Digital Signatures

Provide: **integrity + data authentication + non-repudiation** (sender cannot deny).

**MAC vs Digital Signature:**

| | MAC | Digital Signature |
|----------|-----|-----------------|
| Key type | Symmetric (shared secret) | Asymmetric (private key) |
| Non-repudiation | ❌ No (both parties know key) | ✅ Yes (only signer has private key) |
| Verification | Anyone with secret key | Anyone with public key |

### Signing Process

```
1. Compute hash: h = H(message)
2. Sign hash: sig = Sign(h, private_key)     ← uses private key
3. Send: message ∥ sig
```

### Verification Process

```
1. Compute hash: h' = H(message)
2. Verify: Verify(sig, h', public_key)        ← uses public key
3. If h' matches recovered hash → signature valid
```

> Sign the **hash**, not the full message — asymmetric operations on large data are too slow.

### Common Signature Algorithms

| Algorithm           | Basis                                 |
| ------------------- | ------------------------------------- |
| **RSA-PSS**         | Integer factoring                     |
| **DSA / ECDSA**     | Discrete logarithm / Elliptic curves  |
| **EdDSA (Ed25519)** | Edwards-curve, current recommendation |

---

## Key Size Comparison (Equivalent Security Levels)

| Symmetric | RSA / DH | ECC |
|-----------|----------|-----|
| 80 bits | 1024 bits | 160 bits |
| 112 bits | 2048 bits | 224 bits |
| 128 bits | **3072 bits** | **256 bits** |
| 192 bits | 7680 bits | 384 bits |
| 256 bits | 15360 bits | 521 bits |

**Key insight:** ECC achieves the same security with **much shorter keys** than RSA/DH → faster operations, smaller certificates.

---

# Lecture 11 – Usable Security (Human-Centered Security Design)

> Lecture notes: [[lectures/L11-UsableSecurity|L11]] | Related concepts: [[concepts/Authentication|Authentication]] · [[concepts/Biometrics|Biometrics]]

## Motivational Example: Hawaii Missile Alert (2018)

**What happened:** January 13, 2018 — employee sent real ballistic missile threat alert to all Hawaii residents via SMS. Took **38 minutes** to cancel.

**Cause: Bad warning system UI**
- Single dropdown with real alerts mixed with drill options (e.g., "DRILL-PACOM (DEMO) STATE ONLY" right next to "PACOM (CDW) - STATE ONLY")
- No confirmation dialog before sending
- Difficult to undo: alarm could be turned off but no easy way to transmit custom cancellation message

| | Assessment |
|--|--|
| **Good** | Simple one-click solution to quickly issue threat warning; automatic consequences (sirens, SMS) |
| **Bad** | Messy UI, error-prone, no confirmation dialog, difficult to undo |
| **Ugly** | Who is to blame? Operator? Developer? Designer? → Symptom of lack of usability |

> **Key takeaway:** Bad usability in safety/security systems has real-world consequences. Don't blame the user — improve the system.

---

## 1. Usability & Usable Security

### Usefulness = Utility + Usability (Jakob Nielsen, 1994)

**Usefulness** is composed of:
- **Utility** — Effectiveness: Does the system do what users need? Does the user succeed?
- **Usability** — five dimensions:

| Dimension | Question |
|-----------|----------|
| **Learnability** | How easy is it for users to succeed the first time? |
| **Efficiency** | Having learned the system, how quickly can users perform tasks? |
| **Memorability** | Returning after some time, how easily can users re-establish proficiency? |
| **Errors** | How many errors do users make? How severe? How easy to recover? |
| **Satisfaction** | How pleasant is it to use the system? (inherently self-reported) |

**Context matters:** Different tools for different use cases (e.g., VIM vs. general IDE — VIM has steeper learning curve but higher long-term efficiency).

**Security-specific note:** Security is NOT a primary goal for users — nobody says "I'm going to do some security now!" Security should be like a **display case**: protect without obstructing the view/use.

### Usable Security Definition

> **Usable security (human-centered security)** is a multidisciplinary field investigating and improving system security (and privacy) **focusing on human interaction with the system**.

- Uses methods of HCI, psychology, sociology, design
- Security experts often lack usability skills; usability specialists often lack security skills
- **Core philosophy: Don't blame the user, improve the system instead!**

### Security vs. Usability vs. Usable Security (padlock example)

| Focus | Example questions |
|-------|-------------------|
| **Security** | How sturdy is the lock? How many combinations exist? |
| **Usability** | How easy are the dials to move/read? Are alphanumeric combos more memorable? |
| **Usable security** | What happens if you forget the combination? Do people choose more secure codes with alphanumeric dials? Can you change the combination? Can authorities open it? |

---

## 2. Usable Security for Regular Users

### Example 1: Password Authentication

**What makes a secure password?**
- High entropy ("long")
- Unpredictable
- Don't reuse
- Don't write down
- Change regularly

**Each rule prevents a specific attack:**

| Rule | Attack prevented | Caveat |
|------|-----------------|--------|
| Long | Brute-force | — |
| Unpredictable | Dictionary attack | — |
| No reuse | Credential stuffing from leaked DBs | OK for low-priority accounts (too many passwords today) |
| Don't write down | Physical theft | **BUT**: use a password manager instead |
| Change regularly | Long-term compromise | **BUT**: leads to predictable patterns (higher risk than protection) |

> **Important: Security is a process, not a state** (changes over time).

**Passwords mental models (what users actually think):**
- Brute-force attacks: generally understood
- Dictionary attacks: **often NOT part of mental model**
- Attacks seen as personal ("Why would I be targeted?") — mass automated attacks not understood
- Password re-use attacks: not understood
- Adding "!" at the end considered sufficient
- Leet speak (pa$w0rd) considered secure
- Birthday considered private if not on Facebook

**Security vs. Usability vs. Usable Security for passwords:**
- **Security:** "Make it long and unpredictable!" → e.g., `36zFX_Ga*p#Wbo&2uSf`
- **Usability:** "Make it memorable!" → e.g., `iloveyou`, `123456789`
- **Usable security 1:** Passphrases — memorable AND secure: `king-had-2-sons-and-gave-1/2-a-princess-to-each`
- **Usable security 2:** Password managers — secure passwords without memorization

**Passphrases (xkcd 936):**
- `Tr0ub4dor&3` — ~28 bits entropy, hard to remember ❌
- `correct horse battery staple` — ~44 bits entropy, easy to remember ✅
- *Make passwords easy to remember and hard to guess, not vice versa!*

**How to resolve problematic password authentication:**
1. Train (force) users into security (password requirements UI)
2. **Change the system** (preferred):
   - Single-sign-on (fewer authentications)
   - Auth wallets (centralized to device)
   - Biometrics (nothing to remember)
   - Passkeys (user-friendly auth based on asymmetric crypto)

> **Important: Prefer system usability over user training.**

### Detour: Mental Models

> "A mental model is a user's view (correct or otherwise) of how a system works and the consequences of user actions." — Van Oorschot

Mental model includes: What attacks do users think exist? Who do they think the attackers are? What information do they think is private?

### Example 2: Data Integrity

Verifying data fingerprint via an independent channel (banking, server key, PGP key).

**Evolution of fingerprint representation:**

| Generation | Format | Example |
|------------|--------|---------|
| **Hex fingerprint** | Raw hex in groups of 4 | `A2A9 ADA8 116C DA25 10F4` |
| **PGP words** | Hex mapped to pronounceable words | `topmost Istanbul Pluto vagabond treadmill Pacific` |
| **ASCII image** | Geometric visual representation of SSH key | Random dot pattern in terminal |
| **Robohash** | Hash → unique robot/avatar image | Easy-to-compare visual fingerprints |

Real examples: Element chat (emoji verification), eObčanka (robot image + transaction ID).

> **Important: You (designers) are not your users — different preferences and mental models.**

### Detour: Habituation

**Habituation** = diminishing the response to a frequently repeated stimulus (informally: ignoring things that repeat often).

- **Consequence:** The original purpose (warning) no longer works; changes can happen unnoticed
- **Solution:** Avoid unnecessary use; avoid design that leads to habituation
- Habituation is much faster with **jargon** — ask only what the user can know; use accessible language
- Designer's responsibility: decide WHEN (not) to show a warning

**Good design against habituation (Czech national ID app):**
- Notification only **once** per new app with display-read permission (not every time)
- Dialog buttons require **waiting before clicking** (cannot click-through)

> **Important: Make context-aware decisions.**

### Example 3: AI Security

- Claude in Chrome (beta 2026): Chrome as gateway for AI to act online
- **Attack vector:** Reading a website with hidden prompt injection to exfiltrate financial data from Gmail via MCP
- Anthropic's warnings face challenges:
  - **Habituation** ("Always review" warnings get ignored)
  - Different mental models between developers and users for prompt injection attacks
  - Context-specific risks (MCP, connectors)

---

## 3. Usable Security for IT Professionals

### The Impact Pyramid

```
     OS developers          ← few people, HIGHEST impact per person
   Library developers
  Software developers
 Administrators/IT support
      End users             ← most people, lower individual impact
```

**Usability is even MORE important for IT professionals** — one developer's mistake affects millions of users.

### Areas where IT professionals need usability:
- Command line interaction (commands, error messages, attention management)
- File structure & location (logs, config files, certificates)
- Interfaces (security APIs, cryptographic operations)
- Documentation (security configuration, cryptographic libraries)

### Example 1: SSH CLI

**First connection — host authenticity warning:**
- Presents ECDSA key fingerprint (SHA256 and MD5)
- "Are you sure you want to continue connecting (yes/no)?"
- After accepting: permanently adds to known hosts; shows last login (security indicator)

**Key questions for usable security:**
- What is the actual risk? What happens after (not) agreeing?
- How do you undo adding a known host?
- Why is last login displayed? (security: detect unauthorized access)
- How much "unwanted" information is appropriate?

**Host key changed warning** — much more alarming:
- `WARNING: POSSIBLE DNS SPOOFING DETECTED!`
- `WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!`
- Provides specific file/line of offending key
- Connection refused entirely (no option to bypass easily)

**PuTTY vs SSH CLI comparison:**
- PuTTY GUI: Yes (add to cache), No (connect once without caching), Cancel (abort) — clearer options
- SSH CLI: yes/no only — less clear what "no" does

### Example 2: Security API Configuration

**PayPal SDK (early 2012) using libcurl:**

```php
curl_setopt($ch, CURL_SSL_VERIFYPEER, FALSE)  // disabled cert verification!
curl_setopt($ch, CURL_SSL_VERIFYHOST, FALSE)  // disabled hostname verification!
```

> **Important: Have secure defaults.** The default should be the most secure option.

**Fixed on 27th April 2012:**
```php
curl_setopt($ch, CURL_SSL_VERIFYPEER, TRUE)
curl_setopt($ch, CURL_SSL_VERIFYHOST, TRUE)  // ← STILL WRONG!
```

**The subtle trap:**
- `CURL_SSL_VERIFYPEER` is **Bool** — TRUE = verify ✅
- `CURL_SSL_VERIFYHOST` is **Int** (not Bool!):
  - `0` = no host verification
  - `1` = debug/partial (nearly no verification)
  - `2` = proper hostname verification ✅
- Setting to `TRUE` → evaluates as integer `1` → debug mode, NOT actual verification!

---

## 4. Five Principles of Usable Security Design

### Principle 1: You (designers) are not your users
- Designers and IT professionals are NOT typical end users
- Preferences are subjective; mental models differ
- **User testing is crucial** — beware: self-reported data vs. objective measures

### Principle 2: Prefer system usability over user training
- **Option A:** Train users to behave securely — training costs, variable adherence
- **Option B (preferred):** Improve system usability — explore usable alternatives; higher dev cost, needs user studies
- **Ideal:** Combine both; ratio depends on context

### Principle 3: Security is a process, not a state
- Everything evolves: software, attacks, best practices
- Keeping status quo (usually) means becoming insecure
- Specific challenges: user inertia, historical context, organizational politics

### Principle 4: Make context-aware decisions
- Don't just optimize the warning — consider whether to show it at all
- Example: Revoked certificates don't issue a warning — they **refuse the connection** entirely
- It may be more usable (and more secure) to not ask the user at all

### Principle 5: Have secure defaults
- "The default way should be the most secure way."
- Security is a secondary goal — help securely but don't obstruct use
- "Security should be like a display case: protect, not obstruct view"

---

## 5. Application Examples (Security UX Analyses)

### MUNI IS Login (muni.islogin.cz)
- IS MU login redirects to **different domain** (islogin.cz, not muni.cz)
- **Security reason:** muni.cz has many admins; islogin.cz fully controlled by IS MU; prevents password autofill attacks
- **Usability concern:** Logging in on a different domain violates usual security guidelines (users taught to check domain)
- Change almost transparent for end users
- Worth the trade-off? → Context-aware decision

### MUNI Two-Factor Authentication
- Two **separate** 2FA systems: IS MU (OTP, app, NIA) vs. MUNI SSO (OTP, hardware key, backup codes)
- Different options, different usability
- Root cause: historical development by different teams, different mandates, organizational politics
- Technological unification would be easy — but politics/history stand in the way
- **Illustrates:** Security is a process, not a state; organizational context matters

### IS MU Password Change Screen
- Standard fields: old password, 2× new password, show characters toggle
- Shows **when** current password was set (security indicator for detecting unauthorized changes)
- **Pregenerated password dropdown** — nudges users toward secure options; all are short pronounceable passwords (memorability vs. security trade-off)
- Long textual password guidelines at bottom (with examples and reasoning)

### Keybase Sign-Out Screen
- Before signing out, asks: "Do you know your password?"
- Offers "Test password" (blue/focused button) and "Just sign out" link
- **Rationale:** Chat apps are usually permanently logged in; users may forget rarely-used password
- **Usability concern:** "Test password" screen is unusual; the focused blue button should reflect what users most likely want to do — here it reflects what the developer wants

---

## Algorithm Speed Comparison

| Algorithm | Type | Speed |
|-----------|------|-------|
| **AES-128 CBC** | Symmetric encrypt | ~53–76 MB/s |
| **HMAC-SHA256** | MAC | ~50 MB/s |
| **RSA-2048 encrypt** | Asymmetric | ~7–10 MB/s |
| **RSA-2048 decrypt** | Asymmetric | ~0.5–3.4 MB/s |
| **ECDSA sign** | Asymmetric | faster than RSA |

Symmetric operations are **orders of magnitude faster** than asymmetric — hence hybrid encryption.

### Cost of Brute Force

| Key length | Estimated cost to break |
|------------|------------------------|
| 56 bits (DES) | Trivial — hours |
| 80 bits | ~$10B (feasible for nation-states) |
| 128 bits | Infeasible with current technology |

---

## Case Study: KeeLoq

- Proprietary cipher used in **car remote entry systems** (Microchip Technology)
- **Algorithm kept secret** (violated Kerckhoffs' principle)
- 2008: completely broken — full key recovery possible
- Attacker could **clone car remotes** by observing 64 messages
- Lesson: **Proprietary ≠ Secure; security through obscurity fails**

---

---

# Lecture 3 – Digital Signatures, PKI, eIDAS & Cryptographic Protocols

> Lecture notes: [[lectures/L03-PKI-eIDAS-Protocols|L03]] | Related concepts: [[concepts/Digital-Signatures|Digital Signatures]] · [[concepts/PKI-Certificates|PKI & Certificates]] · [[concepts/Authentication|Authentication]]

---

## Digital Signature – Recap & Key Steps

**Provides:** data integrity + data origin authentication + **non-repudiation** of origin  
**MAC provides:** data integrity + data origin authentication — **NO non-repudiation**

Actual signing = signing the **hash** of data (not the full data — asymmetric crypto is slow)

**Key requirements:**
- **Private key** (signing) — must be kept **confidential**
- **Public key** (verification) — must be **authentic** and available

**4 key steps:**
1. Key generation
2. Public-key certification / signing
3. Signature generation
4. Signature verification

---

## Public Key Ownership & Certificates

A **public-key certificate** binds a public key to an identity, signed by a trusted **Certification Authority (CA)**.

**To issue a certificate, the CA requires:**
- The subject's public key
- Evidence of identity ("X is indeed X")
- Sometimes: online challenge proving private key ownership

**Key property:** Certificate can be used even when the CA is **offline** — it's a signed document.

**Relying party** — trusts values in the certificate to make decisions:
- **TOFU** (Trust On First Use) — liberal approach; trust unless blacklisted
- **COFU** (Check On First Use) — conservative; verify on first encounter

---

## X.509v3 Certificate Fields

| Field | Contents |
|-------|---------|
| `Version` | X.509v3 or other version |
| `Serial-Number` | Uniquely identifies certificate (used for revocation) |
| `Issuer` | Issuing CA's name |
| `Validity-Period` | Not-Before, Not-After dates |
| `Subject` | Owner's name |
| `Public-Key info` | Algorithm + key value |
| Extension fields (optional) | Subject-Alternate-Name, Basic-Constraints, Key-Usage, CRL-Distribution-Points |
| `Signature-Algorithm` | algorithmID, parameters |
| `Digital-Signature` | Signature of Issuer (CA) |

### Extension Fields — Critical vs Non-Critical

- **Non-critical extension:** if unknown, system can ignore it and continue
- **Critical extension:** if unknown, system **must reject** the entire certificate!

**Common extensions:**
- `Key-Usage` — allowed operations (signatures, encryption, key agreement, CRL signing)
- `Extended-Key-Usage` — code signing, TLS server authentication
- `Subject-Alternate-Name` — email, domain, IP, URI; **must be critical if Subject is empty**
- `Name-Constraints` — CA control of Subject names in sub-certificates

---

## Certificate Chain Validation

Trust is established through a **chain from a self-signed trust anchor** (root CA) to the target certificate:

```
[Self-signed Root CA (trust anchor)]
        → signs → [Intermediate CA2 cert]
                        → signs → [Target cert]
                                       → Finally trust target public key
```

- Client starts with a trusted root CA embedded in the OS/browser
- Each certificate in the chain is signed by the CA above it
- **All signatures must validate** for the chain to be trusted

---

## Certificate Revocation

Certificates may need to be revoked before expiry (key compromise, end of employment, etc.)

**Revocation timeline:** key compromised → detected → reported → CA processes → publicly available → relying party sees it  
→ **Delays exist at each step** — OCSP reduces the last delay (T6→T7)

### CRL – Certificate Revocation List

- Published by CA — a **blacklist** of serial numbers
- Contains: `CertificateSerialNumber`, `Time`, (optional extensions)
- **Not a list of certificates** — only serial numbers!
- Updated at periodic intervals → may be stale

### OCSP – Online Certificate Status Protocol

- Real-time query to CA: "is this certificate still valid?"
- Response: **"good"**, **"revoked"**, or **"unknown"**
- **Preferred method** — provides freshness; removes T6→T7 delay vs CRL

---

## Public-Key Infrastructure (PKI)

PKI is the full system for managing key lifecycle:

**Components:**
- **CA** (Certification Authority) — issues and signs certificates
- **RA** (Registration Authority) — handles identity verification on behalf of CA
- **Certificate Directory** — publicly available certificate storage
- **Cert Status Info** — CRL / OCSP services

**Key lifecycle events:**
1. Key loss without compromise → **key recovery**
2. Key compromise or early termination → **key revocation**
3. Private key management → backup & archival

### Private Key Compromise

- **User key compromised:** revoke immediately, inform relying parties, generate new key pair
- **CA private key compromised:** all certificates issued by that CA are suspect — catastrophic
  - CA root keys stored in **safes and bunkers**, encrypted for most of their lifetime
  - Recovery: **threshold cryptography** (t-of-n scheme — need t shareholders to recover the key)

---

## PKI Trust Models

### Hierarchical Trust Model
- **Root CA** at top, **Intermediate CAs** below, **End-entity certs** at leaves
- Single path to root — simple to validate
- All parties must trust the same root

### Browser/Application Trust Model
- Multiple **disjoint hierarchical trees** (CA1, CA2, CA3, ...)
- Root CAs embedded in **browser/OS trust anchor list**
- No cross-certificates between roots — each tree is independent
- Used for HTTPS on the web

### Cross-Certification Models
- **Ring-mesh:** each pair of CAs signs a certificate for the other → scales as O(n²)
- **Bridge-CA (Hub-and-Spoke):** all CAs cross-certify with a central hub → scales as O(n)

---

## Timestamping

- **Timestamp** = proof that data existed at a specific time
- Implemented as: digital signature + reliable clock information
- Issue: **clock synchronization** — attacks possible if clocks are manipulated
- First of the so-called **digital notary services**

---

## eIDAS Regulation

**Regulation (EU) No 910/2014** — on electronic identification and trust services for electronic transactions in the internal EU market  
Replaced: Directive 1999/93/EC and national acts on electronic signatures  
**Updated by:** eIDAS 2.0 — Regulation (EU) 2024/1183

### What eIDAS Covers

**1. Electronic Identification (eID)**
- Enables **mutual recognition of eID** across EU member states
- Promotes digital single market

**2. Trust Services**
- Electronic signature, electronic seal, electronic timestamp
- Electronic registered delivery, website authentication, electronic document

### eIDAS Assurance Levels (Art. 8)

| Level | Requirements |
|-------|-------------|
| **Low** | At least 1 authentication factor; reasonable controls against misuse |
| **Substantial** | At least **2 authentication factors** from different categories; assumed to be under owner's control |
| **High** | Substantial + protects against duplication/tampering; reliable protection against use by others |

---

## Electronic Signature Types (eIDAS)

### A. (Simple) Electronic Signature — Art. 3/10
- Any data in electronic form attached to other data and used to sign
- Examples: name typed in email, scanned signature, click-to-sign
- **Lowest level** — no specific technical requirements

### B. Advanced Electronic Signature — Art. 3/11
- Based on a certificate, but **no requirement on certificate quality** (any cert)
- Uniquely linked to signatory, capable of identifying them
- "Something in between"

### C. Qualified Electronic Signature — Art. 3/12
- Must be based on a **qualified certificate** for electronic signatures
- Must be created using a **qualified signature creation device** (e.g., chip card / eID / token)
- **Highest legal strength**

### Legal Effect (Art. 25 eIDAS)

1. No electronic signature may be denied legal effect **solely** because it's electronic or doesn't meet QES requirements
2. **QES has equivalent legal effect of a handwritten signature**
3. A QES based on a qualified certificate from one Member State **must be recognized in all other Member States**

### eIDAS 2.0 New Features
- **European Digital Identity Wallet (EUIDW)**
- Qualified archivation
- Attributed authentication
- Advanced signatures based on qualified services

---

## Cryptographic Protocols

A **cryptographic protocol** is an algorithm specifying message exchange and actions between parties using cryptographic techniques.

### Types of Protocols

| Category | Sub-type |
|----------|---------|
| **Entity authentication** | Unilateral (one party authenticated) |
| | Mutual (both parties authenticated) |
| **Key establishment** | Key transport — key transferred from one party |
| | Key agreement — key derived from contributions of both parties |

---

## Entity Authentication

**Three principal levels:**

1. **Weak secret** (password)
   - Susceptible to brute force, dictionary, and replay attacks
   - Easy for humans, but low security

2. **Challenge-response with cryptographic key** — most common
   - Key must be stored securely
   - Vulnerable if implementation/parametrization is wrong

3. **Zero-knowledge proof** — theoretically best
   - Proves knowledge without revealing the secret at all
   - Gaining popularity with cryptocurrencies and smart contracts

### Claimant & Verifier
- **Claimant** = party being authenticated
- **Verifier** = party receiving assurances

---

## Challenge-Response Protocols

### Naïve Approaches (all insecure)
1. **Send secret S directly** → verifier learns S, can now impersonate
2. **Send H(S)** → verifier can replay H(S) to impersonate
3. **Send H(r, P) where r = random challenge** → offline guessing attack possible with weak password P

### Challenge-Response with Symmetric Crypto
```
1. A ← B: r_B          (B sends random challenge)
2. A → B: E_K(r_B, "B") (A encrypts challenge with shared key K)
```
- Proves A knows shared key K
- **Reflection attack** possible (B sends challenge back to A and gets valid response)
- **Relay attack** possible (time/distance bounding needed)
- **Critical 3rd party issue** — MAC-based auth cannot provide non-repudiation

### Challenge-Response with Asymmetric Crypto
**Through signing:**
```
1. A ← B: r_B
2. A → B: r_A, "B", Sign_A(r_A, r_B, "B")    (B needs trusted cert_A)
```
**Through decryption:**
```
1. A ← B: h(r), "B", Pub_A(r, "B")            (B needs Pub_A from trusted cert_A)
2. A → B: r
```
Proves A can use the private key corresponding to the certified public key.

---

## Time Variant Parameters (TVPs)

Provide **message uniqueness, freshness, or cryptographic binding**:

| TVP | Properties | Use case |
|-----|-----------|---------|
| **Random number (nonce)** | Unpredictable, never re-used, sufficiently long | Freshness, chaining messages |
| **Timestamp** | Timeliness guarantee; requires synchronized clocks | Time-bounded protocols |
| **Sequence number** | Uniqueness only (not unpredictability) | Message ordering |

---

## Key Establishment

### Session Keys
- **Short-term symmetric keys** — used for one session only
- **Ephemeral keys** — destroyed at session end, unrecoverable
- **Forward secrecy:** if long-term key compromised, past session keys cannot be recovered

### Key Transport Models
- **KDC (Key Distribution Center):** trusted server generates session key K_AB, sends copies to both A and B
- **KTC (Key Translation Center):** A generates session key, server re-encrypts it for B

### Diffie-Hellman Key Agreement (1976)
First published public-key concept. Allows two parties to derive a shared secret over a public channel:

```
Public parameters: prime p, generator g

1. A → B: g^a mod p     (A picks secret a)
2. A ← B: g^b mod p     (B picks secret b)

Both compute: K = g^(ab) mod p
```

**Security:** based on hardness of the **discrete logarithm problem**

**Critical weakness:** unauthenticated DH is vulnerable to **MITM attack** — adversary C intercepts both sides and establishes separate keys K_A and K_B with each party, reading and re-encrypting all traffic.

**Solution:** authenticated DH — bind the exchange to verified public keys (certificates)

---

## Attacks on Authentication Protocols

| Attack | Description |
|--------|-------------|
| **Replay** | Re-use a previously captured message in a later protocol run |
| **Reflection** | Replay a captured message back to the originating party |
| **Relay** | Forward a message in real time from a distinct protocol run |
| **Interleaving** | Weave messages from distinct concurrent protocol runs |
| **Middle-person (MITM)** | Proxy between two end-parties — intercepts and modifies |
| **Dictionary** | Heuristically prioritized list used in guessing attack |
| **Forward search** | Feed guesses into a one-way function, seek output matches |
| **Pre-capture** | Extract client OTPs by social engineering for later use |

**Defense:** use **time-variant parameters** (nonces, timestamps) to cryptographically bind messages within a protocol run.

> Attacker threat model: all bitstrings pass through an attacker-controlled point. Forwarding unchanged ≠ successful attack.

---

---

# Lecture 4 – Cryptographic Protocols II: STS, NS, Kerberos, TLS, SSH, Post-Quantum

> Lecture notes: [[lectures/L04-TLS-SSH-PostQuantum|L04]] | Related concepts: [[concepts/TLS-SSL|TLS/SSL]] · [[concepts/Authentication|Authentication]] · [[concepts/PKI-Certificates|PKI & Certificates]]

---

## Station-to-Station (STS) Protocol

**Turns unauthenticated DH into authenticated DH** using digital signatures.

```
(1) A → B: g^a
(2) A ← B: g^b,  { S_B(g^b, g^a), cert_B }_K
(3) A → B:       { S_A(g^a, g^b), cert_A }_K
```

- K = g^(ab) mod p — derived via DH, **not pre-shared**
- Signatures cover each party's own DH value + the other party's value
- Encryption of signatures with K prevents Mallory from learning identities
- Provides **mutual authentication** and **forward secrecy**
- Classified as: category 6c (key agreement, hybrid, authenticated)

---

## Key Authentication Properties

**Good session key must be:**
- **Fresh** — not reused from a previous session
- **Sufficiently long** — enough bits for the security level required
- **Known with whom it's shared** — bound to a specific identity

### Key Security Properties

| Property | Definition |
|----------|-----------|
| **Forward secrecy** | Disclosure of long-term keys does NOT compromise past session keys |
| **Ephemeral key** | Destroyed at session end — cannot be recovered later |
| **Known-key security** | Compromise of one session key does not endanger future sessions or allow impersonation |

**Implicit key authentication:** party B is assured only the intended party A could have computed K  
**Explicit key authentication:** implicit + **key-use confirmation** (proof that A actually holds K)

---

## Needham-Schroeder Protocols (1978)

Classic paper: *Using Encryption for Authentication in Large Networks of Computers* — Needham & Schroeder, CACM 1978.

### Needham-Schroeder Symmetric Key Protocol

**Goal:** establish session key K_AB between A and B via trusted server S.

```
1. A → S: A, B, N_A
2. S → A: { N_A, K_AB, B, { K_AB, A }_K_BS }_K_AS
3. A → B: { K_AB, A }_K_BS
4. B → A: { N_B }_K_AB
5. A → B: { N_B - 1 }_K_AB
```

**Vulnerability (Denning-Saco, 1981):** Replay attack — attacker M who knows an old K_AB can replay step 3, making B think K_AB is fresh. B cannot detect this without logging all old tickets.

**Fix:** Add a **timestamp** (used in Kerberos) or add a **nonce** from B into the ticket.

Fixed protocol (nonce-based fix):
```
1. A → B: A
2. B → A: { A, N'_B }_K_BS
3. A → S: A, B, N_A, { A, N'_B }_K_BS
4. S → A: { N_A, K_AB, B, { K_AB, A, N'_B }_K_BS }_K_AS
5. A → B: { K_AB, A, N'_B }_K_BS
6. B → A: { N_B }_K_AB          (Note: N'_B ≠ N_B)
7. A → B: { N_B - 1 }_K_AB
```

### Needham-Schroeder Public-Key Protocol

**Goal:** mutual authentication using public-key crypto.

```
1. A → S: A, B
2. S → A: { K_PB, B }_K_SS
3. A → B: { N_A, A }_K_PB
4. B → S: B, A
5. S → B: { K_PA, A }_K_SS
6. B → A: { N_A, N_B }_K_PA
7. A → B: { N_B }_K_PB
```

**Vulnerability (Lowe's attack, 1996):** If Mallory can convince Alice to run the protocol with her, Mallory can use Alice as an oracle to make Bob believe he's talking to Alice (while actually talking to Mallory).

### Lowe's Attack Mechanism
```
1.  A → S: A, M          (Alice asks for Mallory's key)
2.  S → A: { K_PM, M }_K_SS
3.  A → M: { N_A, A }_K_PM
4.  M/A→ B: { N_A, A }_K_PB   (Mallory forwards to Bob!)
5.  B → S: B, A
6.  S → B: { K_PA, A }_K_SS
7.  B → M/A: { N_A, N_B }_K_PA
8.  M → A: { N_A, N_B }_K_PA
9.  A → M: { N_B }_K_PM
10. M/A→ B: { N_B }_K_PB      (Bob now thinks he talked to Alice)
```

### Needham-Schroeder-Lowe Fix

**Fix:** add B's identity into message 6:

```
6. B → A: { N_A, N_B, B }_K_PA     ← added "B" (highlighted)
```

When Alice decrypts step 6, she sees B's name — but the protocol was running with Mallory, not B → she detects the attack.

> **Lesson:** Be as **explicit** as possible in cryptographic protocol messages. Don't optimize for message length — every ambiguity is a potential vulnerability.

---

## Kerberos

**Protocol suite for mutual authentication** over an insecure network. Used in Windows Active Directory, corporate networks.

- Based on **symmetric-key cryptography** (newer versions allow some asymmetric crypto)
- Uses a **trusted third party** (KDC) rather than requiring pairwise shared secrets
- Based on Needham-Schroeder symmetric protocol with timestamps (fixes replay)

### Components

| Entity | Role |
|--------|------|
| **AS** (Authentication Server) | Authenticates client identity |
| **KDC** (Key Distribution Center) | Issues session keys and tickets |
| **TGS** (Ticket-Granting Service) | Issues service tickets based on TGT |
| **TGT** (Ticket-Granting Ticket) | Time-stamped, encrypted with TGS key |

### Simplified Protocol

```
Definitions: L = lifetime; ticket_B = E_K_BT(k, "A", L); auth = E_k("A", T_A)

1. A → T: "A", "B", n_A
2. A ← T: ticket_B, E_K_AT(k, n_A, L, "B")
3. A → B: ticket_B, auth
4. A ← B: E_k(T_A)
```

- T = KDC; B = TGS (Ticket-Granting Service)
- Client gets ticket from KDC and presents it to the service — **KDC need not be online** after step 2
- Timestamps prevent replay (instead of nonces) — clocks must be synchronized (~5 min tolerance)

---

## Transport Layer Security (TLS)

TLS is the security protocol underlying HTTPS, email (SMTP/POP3), VPN, VoIP, etc.

### TLS History

| Version | Year | Notes |
|---------|------|-------|
| SSL 2.0 | 1995 | First public release; many flaws; deprecated 2011 |
| SSL 3.0 | 1996 | Deprecated 2015 |
| TLS 1.0 | 1999 | First IETF standard |
| TLS 1.3 | 2018 | Current standard; major redesign |

**Author of SSL:** Taher ElGamal (Netscape Communications) — "father of SSL"

**Position in stack:** Above TCP, below application layer (HTTP, SMTP, etc.)

### TLS Protocol Structure

**Handshake layer** — security parameter setup at session start:
- Key exchange (authenticated)
- Server authentication (and optionally client auth)
- Setting of server-selected parameters
- Design intent: **limit attacker influence** on parameters; **fail completely** if attack detected

**Record layer** — protects application data using parameters from handshake

### What TLS Provides

| Property | Mechanism |
|----------|---------|
| **Confidentiality** | Encryption (AES-GCM or ChaCha20) |
| **Integrity** | AEAD (TLS 1.3); MAC (older versions) |
| **Entity authentication** | Default: unilateral (server only); mutual is rare |

### TLS 1.3 Handshake (simplified)

```
1. ClientHello → (offered versions, algorithm list, client nonce, client key share/PSK)
2. ServerHello ← (server nonce, key share, selected options, server cert + signature, Finished MAC)
3. ClientAgain → (client cert if requested, client Finished MAC)
   ──────────────────────────────────────────────────────
   Application Data (both directions, secured by AEAD)
```

**3 phases in ServerHello (B):**
- **(A)** Key exchange phase — key share
- **(B)** Server parameters — selected connection options (highlighted — sent encrypted)
- **(C)** Authentication phase — certificate + signature + Finished MAC

### Key Exchange Options in TLS 1.3

| Method | Description |
|--------|-----------|
| **DHE** (ephemeral DH) | Using finite fields (FFDHE, discrete log problem) or elliptic curves (ECDHE) → provides forward secrecy |
| **PSK** (pre-shared key) | Long-term secret established out-of-band; no forward secrecy alone |
| **PSK + DHE** | Combines both — achieves forward secrecy with PSK optimization |

**Server authentication:** RSA signature or ECC (ECDSA) over the handshake transcript; public key in X.509 certificate

### Resumed Sessions

Asymmetric operations are expensive → TLS allows session resumption:
- After successful handshake, server sends `new_session_ticket` to client
- Ticket contains session ID (identifying a PSK)
- Client includes ticket in next `ClientHello` → skip full handshake

### Ciphers Used in TLS Record Layer

- **AES-GCM** / **AES-CCM** — block cipher with AEAD-integrated MAC
- **ChaCha20-Poly1305** — stream cipher + Poly1305 MAC

### 2 Common Myths About TLS

1. **"TLS provides non-repudiation"** — FALSE. Integrity uses symmetric MAC (both sides know the key) → no non-repudiation
2. **"TLS uses digital signatures for data protection"** — FALSE. Signatures used only for: (a) proving private key knowledge in handshake; (b) certificates. Application data integrity = symmetric MAC

### TLS Attacks

| Attack | Description | Example |
|--------|-------------|---------|
| **Implementation bug** | Flaws in code expose private keys or data | **Heartbleed** (OpenSSL) — read server memory |
| **Downgrade** | Force server to use older, weaker algorithms | **Logjam** (512-bit DH); **DROWN** (SSLv2 fallback) |
| **Padding oracle** | Server responds differently to padding variations | **Lucky Thirteen** |
| **TLS interception (MITM)** | Rogue certificate not properly verified | ISP/corporate proxy intercepts encrypted traffic |

---

## Secure Shell (SSH)

SSH provides an **encrypted tunnel** over an unsecured network — client gets a shell on a remote host.

### SSH Protocol Stack (3 layers)

**1. Transport layer protocol**
- **Server authentication** (to client):
  - Client maintains a local database of server keys — verifies fingerprint on first connection (TOFU)
  - Or: CA-certified server keys
- Negotiation of crypto parameters and session keys
- Encryption and integrity protection

**2. User authentication protocol** (client → server)
- Runs over the transport layer (already encrypted)
- Authentication options:
  - **Password** (static or OTP)
  - **Kerberos ticket**
  - **Client public key** (most secure — challenge-response with private key)

**3. Connection protocol**
- Multiplexes one SSH connection into multiple logical channels (shell, file transfer, port forwarding)

---

## Cryptography in the Quantum Computer Era

### Hard Problems Underlying Current Public-Key Crypto

| Problem | Used in | Quantum threat |
|---------|---------|---------------|
| **Integer factorization** | RSA, Rabin | Broken by Shor's algorithm |
| **Discrete logarithm** | DSA, ECDSA, ElGamal, DH | Broken by Shor's algorithm |

**Shor's algorithm** computes the period of a function efficiently on a quantum computer:
- Factorization: f(x) = a^x mod N has period = **order of element a mod N** (divides φ(N) = (p−1)(q−1), but ≠ φ(N) in general) → recovers p, q
- DLP: uses 2-variable function f(y,z) = g^y · h^z mod p

### Impact on Cryptographic Algorithms

| Algorithm type | Quantum impact | Mitigation |
|---------------|---------------|-----------|
| **Symmetric ciphers** (AES) | Grover's algorithm halves effective key length | **Double key length** (AES-256 → still secure) |
| **Hash functions** | Grover's algorithm reduces collision search | **Longer output** (use SHA-256+) |
| **RSA / ECC / DH** | **Broken** by Shor's algorithm | Replace with **post-quantum algorithms** |

### Post-Quantum Cryptography (PQC)

NIST standardized PQC algorithms in 2024:

| Hard problem | Algorithms |
|-------------|-----------|
| **Shortest vector in lattice** | NTRU, **Kyber** (CRYSTALS-Kyber — NIST KEM standard) |
| **Decoding problem** (linear codes) | McEliece |
| **Hash-based** | SPHINCS+, Merkle tree signatures |
| **Supersingular elliptic curves** | SIDH (broken 2022!), others |
| **Multivariate polynomial systems** | Rainbow (broken), others |

> **Practical short-term mitigation:** Add a layer of symmetric crypto (not affected by quantum) on top of existing protocols.

---

## Case Study 1 – Bias in RSA Public Keys

RSA primes p, q **should be uniformly random** — keys from different implementations should be indistinguishable.

**Reality:** In practice, different libraries make implementation-specific choices (efficiency, shortcuts) that introduce **statistical bias** in the most-significant bits of primes.

**Impact of biased primes:**
- Biased MSB of p, q → **biased public modulus n** = p · q
- Different libraries produce visually distinct "fingerprints" in (p, q) distribution heatmaps
- Enables:
  - **Library identification** — determine which library generated a key from the public key alone
  - **Information leakage** (OpenSSL) — can tell which library was used
  - **Auditing** — identify libraries used in a target organization (e.g., Estonian eID card keys)
  - **Forensics** — attribute keys to devices/libraries
  - **Weak key search** — find all keys from a vulnerable library quickly

---

## Case Study 2 – ROCA Vulnerability (2017)

**Infineon Technologies** tried to speed up RSA prime generation on hardware tokens (smart cards, TPMs, YubiKeys).

**The flaw:** Primes were generated in a special form:

```
Prime_Infineon = k * M + 65537^a mod M
```

This form reduces the prime pool → introduces detectable structure in the public modulus n.

**Impact:**
- **512-bit and 1024-bit** RSA keys: factorized in practice at Masaryk University
- **2048-bit** RSA keys: factorized by Estonian RIA (April 2018, cost "several thousands of euros")
- Affected: Estonian eID cards, Slovak eID cards, Slovak ID documents
- Millions of keys vulnerable

**Lesson: Follow the standards!** Optimization of prime generation must preserve full entropy.

---

---

# Lecture 5 – Identity and Access Management

> Lecture notes: [[lectures/L05-IAM-Biometrics|L05]] | Related concepts: [[concepts/Biometrics|Biometrics]] · [[concepts/Authentication|Authentication]] · [[concepts/PKI-Certificates|PKI & Certificates]]

---

## Identity — Core Concepts

**Identity:** any subset of attributes associated with a given individual that **uniquely identifies** that individual within a given set.

- An individual has **multiple identities** depending on the context/environment
- A **partial identity** is associated with a given context, role, or limited set of individuals

### Attribute Categories

| Category | Sub-types |
|----------|----------|
| **Domain** | Work, university, medical, government |
| **Functional** | Locality, social groups, biological |
| **Temporal – Permanent-given** | Sex, eye colour, parents, date of birth |
| **Temporal – Permanent-gained** | Qualification, behaviour |
| **Temporal – Permanent-situational** | Address, marital status |
| **Temporal – Transitional** | Location, hairstyle, dress style |

### Digital Identity Attributes

Physical entities are represented as a set of digital attributes:
- Email address, nicknames, cell phone number
- Electronic documents, photos, fingerprints
- Passwords, PINs, access cards

---

## Methods of Authentication (4 Factors)

| Factor | Examples |
|--------|---------|
| **Something we know** | Passwords, PINs, passphrases |
| **Something we have** | Hardware token, smart card, cell phone |
| **Something we are** | Biometrics (fingerprint, face, iris) |
| **Somewhere we are** | Geolocation (GPS), co-location (proximity to devices) |

- **Multi-Factor Authentication (MFA):** combination of methods from **at least two** different categories
- **2FA in banking** became prominent due to European legislation (PSD2)
- **SMS is NOT considered strong authentication** (no longer recommended by NIST SP 800-63B) — SIM swapping, SS7 attacks

---

## Passwords

### Password Lifecycle

Choose → Commit → Live with → (Forget → Reuse) → Change → repeat

- **Periodic forced rotation** is generally **bad** — users just make minor variations (password1 → password2)
- **Complexity enforcement** has mixed results — often leads to predictable patterns

### Attacks on Passwords

**Direct attacks:**
- **Online guessing** — try passwords against a live service; protected by account lockout (but recovery mechanisms are often weak)
- **Offline guessing** — crack a stolen password database hash
- **Password capture** — keyloggers, sticky notes, stolen devices

**Bypass attacks:**
- **Password interface bypass** — exploit another path into the system
- **Defeating recovery mechanisms** — security questions, attacking the linked email account instead of the primary service

### Password Best Practices

- Use **unique password per service**
- Use **complex passwords** (mixed character sets) or **passphrases** (long, single character set — length is the key factor)
- If reusing passwords: have a **strong, distinct password for email** (email = master key to all other accounts)
- Use **MFA** on critical services
- Use a **password manager**

### Password Storage on Servers

Passwords are stored **salted and hashed** — NOT encrypted.

**Why not encrypt?**
- Encryption is reversible — if the encryption key is compromised, all passwords are exposed
- Hash is one-way — compromising the DB reveals only hashes, requiring cracking per-password
- **Password managers** work differently: they need to *retrieve* the plaintext → encryption is appropriate there

### Password Managers

**Benefits:**
- Unique password per service
- High-complexity random passwords
- Easy rotation

**New risks:**
- Single point of failure
- Weak master password → online or offline guessing attack

### Single Sign-On (SSO)

- One authentication credential for multiple services/resources
- User authenticates once → receives a token valid for multiple services
- Users accept MFA more readily when they only need to do it once
- Reduces password exposure across services

---

## Hardware Tokens and OTP

**Why tokens?** Passwords are **static** → vulnerable to **replay attacks**.

**One-Time Passwords (OTP):** valid for a single use only.

**OTP delivery methods:**

| Method | Status |
|--------|--------|
| **SMS** | No longer recommended by NIST — SS7 vulnerabilities, SIM swap |
| **Hardware token** (e.g., RSA SecurID) | Dedicated device, strong |
| **Authenticator app** (Google, Authy, etc.) | Phone-based, convenient |
| **FIDO2 / hardware key** (YubiKey) | Phishing-resistant, strongest |

### Challenge-Response OTP Protocols

| Variant | Requirement |
|---------|-------------|
| **Random number-based** | Good source of randomness |
| **Sequence number-based** | Initialization of a sequence; synchronized counter |
| **Timestamp-based** | Synchronized clocks between token and server |

---

## Biometrics

### Two Modes of Deployment

1. **Authentication (verification)** — verify whether a person is who they claim to be → **1:1 comparison** (registered template vs. new sample)
2. **Identification** — determine who a person is → **1:many** search through entire database

### Key Properties of Biometrics

- **Biometrics are NOT secrets** — must be combined with other factors
- **Not suitable for remote authentication** by themselves → remote biometric auth is **two-staged**: user-to-device (biometric), device-to-service (cryptographic)
- **Biometrics of the same person are never 100% identical** → threshold matching is required
- Fallback mechanisms are always necessary

### Evaluation Criteria for Biometrics

| Criterion | Question |
|-----------|---------|
| **Universality** | Do all users have this trait? |
| **Distinguishability** | Are there enough differences between users? |
| **Ease-of-sampling** | How easy to acquire a sample? (fingerprint vs. DNA) |
| **Cost** | Processing time, storage, hardware/software |
| **User acceptance** | Will users accept it? |
| **Attack-resistance** | How hard to circumvent? |

### Enrollment Process

- **Failure to Enrol (FTE):** unsuccessful attempt to register a template (e.g., no readable fingerprint)
- **Failure to Capture (FTC):** bad quality sample during authentication
- After multiple failures: **lock** or **fallback** to backup authentication

### Verification: Error Rates and Threshold

The system computes a **matching score** against the stored template:

- **FRR (False Rejection Rate)** — legitimate user rejected (too strict threshold)
- **FAR (False Acceptance Rate)** — impostor accepted (too lenient threshold)
- Threshold is **configurable** — adjusted based on security sensitivity of the system
- Lowering threshold (lenient) → **more FAR** (more impostors accepted), **less FRR** (fewer real users rejected)
- Raising threshold (strict) → **less FAR**, **more FRR** — and vice versa
- **Equal Error Rate (EER):** point where FAR = FRR — used for comparison

---

## Fingerprint Biometrics

### Feature Levels

| Level | Features |
|-------|---------|
| **Level 1** | Global pattern: arch, tented arch, left/right loop, double loop, whorl |
| **Level 2** | Minutiae: line-unit, line-fragment, ending, bifurcation, eye, hook |
| **Level 3** | Fine details: pores, line shape, incipient ridges, creases, warts, scars |

**Minutiae** = ridge characteristics that identify specific points on a fingerprint.  
Processing pipeline: Biometric sample → extract Minutia Points → build Minutia Map → compare with template.

### Fingerprint Sensor Types

| Type | Mechanism |
|------|----------|
| **Optical** | 2D image capture; oldest technology; easily fooled by fake fingers |
| **Capacitive** | Arrays of tiny capacitor circuits — ridges change charge (contact), valleys do not (air); material matters |
| **Ultrasonic** | Ultrasonic signal return time; typically in-display sensors; more resistant to fake fingers |
| **Thermal** | Temperature differences between ridges and valleys |

**Note:** same sensor type does NOT imply the same scan output.

**Smartphone fingerprint readers:**
- Partial scanning → fewer unique features
- Liveness detection remains an issue

### Attacks and Liveness Detection

**Attacks:** latent fingerprints (lifted from surfaces), replay attacks, fake/spoofed fingers (gelatin, silicone)

**Liveness detection:** testing the finger's reaction to sensor stimuli:
- Temperature measurement
- Skin resistance (electrical properties)
- Pulse / blood flow

---

## Face Recognition

### Approaches

| Type | Examples |
|------|---------|
| **Statistical methods** | Eigenface, PCA, LDA |
| **Neural networks** | Microsoft Face API, Facebook DeepFace, VK FindFace, Google FaceNet |

**OpenFace pipeline:** Input image → Detect face → Transform (alignment) → Crop → Deep Neural Network → 128D unit hypersphere representation → Classification / Similarity detection / Clustering

### Challenges in Face Recognition

- Illumination variation
- Pose variation
- Environment (noisy background)
- Aging
- **Feature occlusion** (hats, glasses, hair)
- Image quality (resolution, colour)

### Attacks on Face Recognition

- **CV Dazzle** (Adam Harvey): specific make-up/hair patterns that defeat face *detection* algorithms (no face detected → no recognition)
- **Adversarial glasses** (Carnegie Mellon, 2016 — "Accessorize to a Crime"): specially printed glasses fool DNN-based face recognition with >90% success rate — "physically realizable and inconspicuous"
- **Privacy concern:** DNNs can infer sensitive attributes from face images (e.g., Wang & Kosinski 2018 showed neural networks can detect sexual orientation from photos at 81%/71% accuracy vs. human 61%/54%)

### Commercial vs. Forensic Biometric Systems

| | Commercial | Forensic |
|---|-----------|---------|
| **Precision** | Lower | High |
| **Enrollment** | Can repeat | Once only |
| **Data stored** | Only extracted features/template | Full raw biometric data |
| **Speed** | Fast, automatic | Slower, expert review needed |
| **Cost** | Cheap | Costly |

---

## Authorization and Access Control

**Authorization:** deciding whether a requested privilege or resource access should be **granted** to the requesting identity.

**Authentication comes first** — you must know who someone is before you decide what they can do.

### Core Principles

- **Default-deny:** unless explicitly permitted, access is **not allowed**
- **Least privilege:** a person or system should be granted the **minimal access necessary** to complete their purpose

### Access Control Models

| Model | Description |
|-------|-------------|
| **RBAC** (Role-Based) | After authentication, user is assigned role(s) with permissions; subject → role → (object + operations) |
| **MAC** (Mandatory Access Control) | Rules defined by **system administrators** — users cannot override |
| **DAC** (Discretionary Access Control) | Rules defined by **resource owners** — more flexible |

---

## Identity Management (IdM / IAM)

**IdM (Identity Management):** organizational process for identifying, authenticating, and authorizing individuals or groups to access applications, systems, or networks by associating user rights with established identities.

**IAM (Identity and Access Management):** framework of policies and technologies ensuring proper people in an enterprise have appropriate access to technology resources.

### PKI in Identity Management

PKI is used to identify not just users but also **services and servers**:
- **X.509 certificates** verify claims about a system (owner, organization, domain name, email)
- Signed by a Certification Authority (CA)
- Used for remote access, financial transactions, etc.

---

## SAML (Security Assertion Markup Language)

- Currently **version 2.0**
- **XML-based** standard for exchanging authentication (AuthN) and authorization (AuthZ) data
- Uses **security tokens containing assertions**
- Primary use: implement **SSO (Single Sign-On)**

### SAML Protocols

- **Authentication Request Protocol**
- **Assertion Query and Request Protocol**
- **Single Logout Protocol**

### SAML SP-Initiated SSO Flow

1. User requests resource from **Service Provider (SP)**
2. SP redirects user to **Identity Provider (IdP)** (SSO service)
3. User authenticates with IdP
4. IdP sends XHTML form with SAML assertion
5. User's browser POSTs assertion to SP's **Assertion Consumer Service**
6. SP validates assertion → grants access → redirects to target resource

---

## SPML (Service Provisioning Markup Language)

- Used to **set up user services and interfaces**
- Provisioning requests carry required information for each service
- Automates **provisioning and deprovisioning** processes
- Together with SAML, creates a full **IdM environment**

---

## OAuth 2.0

Published 2012. Protocol for **delegated authorization** — allows a client app to access resources on behalf of a user without sharing credentials.

**OAuth2 flow (simplified):**
1. User accesses client app
2. Client app redirects user to identity provider (Google, Facebook, etc.) with client ID
3. User authenticates with identity provider
4. IdP redirects user back with an **authorization code**
5. Client app sends auth code + client credentials to authorization server
6. Authorization server returns an **access token**
7. Client app uses access token to access resources on behalf of user

---

## Other IdM Protocols and Systems

| System/Protocol | Year | Type | Description |
|----------------|------|------|-------------|
| **LDAP** | 1993 | Protocol | Lightweight Directory Access Protocol — directory service |
| **Active Directory** | 1999 | System | Originally AD for Windows; evolved into full IAM (Microsoft) |
| **Kerberos v5** | ~2000 | Protocol | AuthN and AuthZ; used inside AD; covered in L04 |
| **OpenID Connect** | 2005 | Protocol | Identity layer on top of OAuth2 |
| **FreeIPA** | 2011 | System | RedHat open-source IdM: combines Kerberos, LDAP, DNS, PKI, NTP |
| **OAuth2** | 2012 | Protocol | Delegated authorization |

### FreeIPA Key Features

- Integrated: Fedora Linux + 389 Directory Server + MIT Kerberos + NTP + DNS + Dogtag CA + SSSD
- Full **multi-master replication** for redundancy and scalability
- Extensible via CLI, Web UI, XMLRPC/JSONRPC API, Python SDK
- Can build **trust between Linux and Windows** environments

---

## Standards and Regulations

### NIST SP 800-63 — Digital Identity Guidelines

Published June 2017. Defines three assurance levels used independently:

| Level Type | What it measures |
|-----------|-----------------|
| **IAL** (Identity Assurance Level) | Confidence in the **claimed identity** (covered in SP 800-63A) |
| **AAL** (Authenticator Assurance Level) | Strength of the **authentication process** (covered in SP 800-63B) |
| **FAL** (Federation Assurance Level) | Strength of **federation assertions** (covered in SP 800-63C) |

**SP 800-63B** is the key document for authentication — it deprecated SMS OTP as a second factor.

### EU eIDAS (in IdM Context)

- **Regulation 910/2014** — covers electronic identification (eID) AND trust services
- **Main goal:** interoperability and transparency across EU member states
- Covers: e-signatures, certificates, e-seals, timestamps
- Technically vague in implementation details (delegates to member states)
- Connects to authentication: eIDAS assurance levels (Low / Substantial / High) map to authentication strength

---

---

# Lecture 6 — Privacy Introduction

> Lecture notes: [[lectures/L06-Privacy|L06]] | Related concepts: [[concepts/Privacy-Anonymity|Privacy & Anonymity]] · [[concepts/GDPR|GDPR]]

## What is Privacy?

- **Vague definition:** quality or state where one is (a) not observed and (b) not disturbed by other people
- **Wikipedia:** ability of an individual or group to seclude themselves or information about themselves, and thereby express themselves selectively
- Applies to both physical and virtual space
- **Information privacy:** concerned with control of information, namely personal data and other sensitive information; an exact definition is illusory due to subjectivity and relation to other individual rights

### Private Information

> "Such information where subjects (people) generally mean they do not want the information shared with others or they personally would like to control the dissemination of this information, sharing it with some but not with others."
> — KC Laudon, Communications of ACM 9/96

### Personal Data

- **Definition:** any information relating to an *identified* or *identifiable* natural person (detailed GDPR definition in Lecture 7)
- The same data item can sometimes identify a person and sometimes not
- **Context matters a lot** — creates issues with de-identification of data
- Example: Is `Name.Surname@SomeCompany.cz` personal data? It depends on context

---

## 4 Privacy Functionalities (Pfitzmann & Hansen Terminology)

Information privacy can be broken into 4 concrete **privacy functionalities**:

| # | Functionality | Definition |
|---|--------------|-----------|
| 1 | **Anonymity** | System supports use of resources/services without revealing user identity. Not protecting the subject in a computer system. |
| 2 | **Pseudonymity** | System supports use of resources/services without revealing identity, yet with accountability (identity can be found out). |
| 3 | **Unlinkability** | User may make multiple uses of resources/services without others being able to link these uses together. |
| 4 | **Unobservability** | User may use a resource/service without others (especially third parties) being able to observe that the resource/service is being used. |

### Anonymity — Details

- **Extremely rare** in practice — full anonymity requires a completely isolated system with no interaction with outside environment
- In practice: either conditional/partial anonymity, or false claims (PR)
- **Anonymity set:** the set of all possible subjects who might cause an action; anonymity is always relative to the anonymity set
- Note: a bigger anonymity set does not necessarily mean better anonymity quality

### Pseudonymity — Details

- Very practical; acknowledged in academic literature and in computer systems for decades; acknowledged by legislation (GDPR) only recently
- **Physical world example:** PO Box
- **Virtual world example:** user-selected ID

### Unlinkability — Details

- Covers uses both in sequence and in parallel, both same and different resources/services
- Does not consider user identity — focuses on the *scope of usage* by the same user
- **IOIs (Items of Interest):** subjects, messages, actions — unlinkability means the attacker cannot sufficiently distinguish whether IOIs are related or not

### Unobservability — Details

- Protected values are not data *about users*, but about *usage of resources and services*
- Methods of protection against traffic analysis serve as examples
- **Key implication:** with respect to the same attacker, unobservability ⇒ anonymity

---

## Common Criteria (CC) Privacy Components

**Common Criteria** = large standard for security evaluation of IT systems/products; enables comparison of systems w.r.t. security and specification of desired functionality.

**CC terminology:**
- **TOE** (Target of Evaluation) — the entire evaluated system
- **TSF** (TOE Security Functions) — HW, SW, FW used by TOE
- **TSC** (TSF Scope of Control) — interactions under the TOE security policy

**CC approach to privacy:** existential view — a property either is or is not present. CC does not consider *how* a property was achieved; evaluation is discrete (Y/N); granularity only by specified levels.

**CC privacy components (FPR family):**

| CC Component | Levels | Key requirement |
|-------------|--------|-----------------|
| **FPR_ANO** Anonymity | 2 | L1: others cannot determine user identity bound to operation; L2: TSF does not ask for user identity |
| **FPR_PSE** Pseudonymity | 3 | L1: accountability maintained; L2: reversible pseudonymity (TSF can determine original identity); L3: alias pseudonymity (construction rules for alias) |
| **FPR_UNL** Unlinkability | 1 | Users/subjects unable to determine whether same user caused certain operations |
| **FPR_UNO** Unobservability | 4 | L1: cannot determine if operation is performed; L2: avoid concentration of privacy info; L3: TSF does not solicit privacy info; L4: authorised user can observe usage |

---

## Mix Networks (Chaum 1981)

**Mix networks** = systems for message transfer providing anonymity via:
- Dummy messages (camouflage traffic)
- Non-optimal redirections and routes (to confuse observer/attacker)
- Defined by David Chaum in 1981 — the most important representatives of the Pfitzmann & Hansen terminology approach

**Mix communication system model:**
- The system has a *surrounding* — parts of the world are "outside" the system
- System + surrounding = the *universe*
- State of the system may change by actions within the system
- Attacker perspective: may be an **outsider** tapping communication lines OR an **insider** controlling some stations

**Pseudonym types (from most linkable to most anonymous):**

```
person pseudonym
    ├── role pseudonym
    └── relationship pseudonym
              └── role-relationship pseudonym
                          └── transaction pseudonym
                          (most unlinkable → most anonymous)
```

| Pseudonym Type | Scope | Linkability |
|---------------|-------|------------|
| **Person pseudonym** | Tied to a person across all contexts | Highest — most linkable |
| **Role pseudonym** | Tied to a role (e.g., "admin") | High |
| **Relationship pseudonym** | Tied to a specific relationship | Medium-high |
| **Role-relationship pseudonym** | Tied to role within a relationship | Medium |
| **Transaction pseudonym** | Single-use, per transaction | Lowest — most anonymous |

- Ongoing use of the *same* pseudonym allows the holder to establish a reputation
- **Pseudonymity** covers the entire spectrum between accountability (fully linkable) and anonymity (fully unlinkable)
- Anonymity is stronger the less is known about the linking to a subject

**Digital pseudonym:** a bit string that is (1) unique as identifier with very high probability, and (2) suitable for authenticating the holder's IOIs (e.g., messages sent)

---

## Value of Privacy — Experimental Studies

### Study 1: FIDIS Mobile Location Study

**Research question:** How much are people willing to be paid for being tracked 24/7 via mobile phone (location tracking)?

**Setup:**
- Organised within FIDIS project
- 5 countries: Belgium, Czech Republic, Germany, Greece, Slovakia
- Target populations: IT students (all countries), general university students (CZ/DE/SK), mobile phone communities (CZ/DE)
- 3 auction rounds: (1) academic use 1 month, (2) commercial exploitation, (3) commercial use 1 year

**Key findings:**

| Finding | Detail |
|---------|--------|
| Drop-out rates | Early: 6–25%; Standard: 44–68% depending on country |
| 2nd bids vs 1st bids | ~2.5× higher |
| 3rd bids vs 2nd bids | ~2× higher |
| Gender difference | Men bid 1.4–1.8× higher than women (medians) |
| Non-linearity in time | 12× longer tracking → only 2× higher bids; data after 1st month of less value |
| Participation motivation | Money (38%), results (32%), fun (30%) |
| Cambridge study comparison | Medians correspond: €43 to £28 |

- 10% of participants bid < 1 EUR (curiosity / enthusiasm)
- Greek participants more sensitive (eavesdropping scandal shortly before)
- No correlation between bids and movement frequency

### Study 2: Email/Messaging Tracking Study

**Research question:** How much to be paid for 2-week tracking of email, instant messaging, or all data?

**Three scenarios:** academic use → commercial use → governmental use (terrorism detection)

**Results (median bids in EUR, 2-week tracking):**

| Scenario | Email | Messaging | All data |
|----------|-------|-----------|---------|
| Academic | ~30 | ~30 | ~50 |
| Commercial | ~40 | ~40 | ~50 |
| Governmental | ~50 | ~50 | ~60 |

- No considerable differences between males and females
- Increasing tendency to opt out as purpose becomes more sensitive
- ~10% dropped academic→commercial (real dropout 27%); ~20% dropped commercial→governmental (real dropout 28%)
- No significant difference in valuation of email vs. instant messaging data

### Study 3: 2017 FI Micro Study (Dark Web Prices)

**Setup:** Undertaken with PV080 enthusiasts; solicited offers for personal data on dark web using fake identities, verified physical addresses, reported sellers to national Data Protection Office

**Dark web prices for personal data (Czech Republic):**

| Data type | Price per subject |
|-----------|-----------------|
| Physical address + phone + email | ~0.15 CZK (15 hellers) |
| National ID number (rodné číslo) or ID card number | ~5.17 CZK |

---

---

# Lecture 7 — Personal Data Protection, Legal and Ethical Aspects

> Lecture notes: [[lectures/L07-GDPR-Ethics|L07]] | Related concepts: [[concepts/GDPR|GDPR]] · [[concepts/Privacy-Anonymity|Privacy & Anonymity]]

## Privacy Protection vs. Personal Data Protection

- **Privacy** = space for development of unique identity, private opinion, dissent
- ICT multiplied challenges to privacy → need for a **preemptive** protection framework
- **Personal data protection** = rules for data-based operations with possible risks/harms to individuals
  - Harms include: invasion of privacy, injury to honor/dignity, manipulation through profiling, discrimination, extortion, identity theft
- Key instrument: **GDPR** — General Data Protection Regulation 2016/679
- "Forcing" the data subject's perspective into business structures — embedding trust, limits on profiling, transparency, individual control

---

## GDPR — Core Terminology (Article 4)

| Term | Definition |
|------|-----------|
| **Personal data** | Any information about a natural person (**data subject**) by which they can be identified, directly or indirectly. Contextual concept — recent tendency towards subjective view (depends on capability of the controller to identify the person). |
| **Processing** | Any structured or (semi)automated operation on personal data |
| **Controller** | Entity that **initiates** the processing of personal data |
| **Processor** | Entity that **carries out** processing at the initiative of another (the controller) |
| **Employee** | Follows instructions of controller/processor (not an independent role) |

---

## GDPR Processing Principles (Article 5)

Processing must be:

1. **Fair, lawful, and transparent**
2. For **specific, explicit and legitimate purposes** (purpose limitation)
3. **Proportionate** — relevant and limited to what is necessary (data minimisation)
4. **Accurate** and, where necessary, updated
5. Stored for **no longer than necessary** (storage limitation)
6. Processed with **appropriate security** (integrity and confidentiality)

**Accountability:** the controller is responsible for compliance and must be able to **demonstrate** such compliance.

---

## Lawfulness of Processing (Article 6)

Lawful processing requires: **purpose** + **scope** + **legal basis**.

**Permissible legal bases:**

| Legal Basis | Description |
|------------|-------------|
| **Contract** | Necessary for the performance of a contract |
| **Legal obligation** | Necessary for compliance with a legal obligation |
| **Legitimate interests** | Necessary for the controller's/third party's legitimate interests (must balance against data subject's rights) |
| **Public interest / official authority** | Necessary for public interest task or exercise of official authority |
| **Vital interests** | Necessary to protect the vital interests of the data subject |
| **Consent** | The data subject has given consent to the processing |

---

## Sensitive Personal Data — Stricter Regime (Articles 9–10)

- Misuse may be particularly serious for the person concerned
- **Default: prohibition of processing**, with narrow exceptions:
  - **Explicit consent**
  - Data manifestly disclosed by the data subject
  - Necessary for establishment/exercise/defence of **legal claims**
  - Partial exemptions for: vital interests of incapacitated persons, labour/social law, research, non-profit bodies, important public interest, medical examinations, public health protection, archiving

---

## Rights of the Data Subject (Chapter III)

| Right | Article | Description |
|-------|---------|-------------|
| **Right to information** | Art. 13–14 | Transparency — what data, why, by whom, how obtained |
| **Right of access** | Art. 15 | Info on request + free copy; must not affect rights of others |
| **Right to rectification** | Art. 16 | Inaccuracies corrected/completed without delay |
| **Right to erasure** ("to be forgotten") | Art. 17 | Deletion — except when necessary for freedom of expression, legal obligation, public authority, archiving, legal claims |
| **Right to object** | Art. 21 | Object to processing for direct marketing, public interest, official authority, or legitimate interests |
| **Notification** | Art. 19 | Controller must notify recipients of rectification, erasure, or restriction |

---

## Controller's Duties (Chapter IV) — Practical Checklist

**Part I:**
- **SCOPE** — identify what constitutes personal data and how it is processed
- **PURPOSE** — be able to state why data is processed
- **BASIS** — identify the legal basis; determine whether consent is needed
- **TRANSPARENCY** — include provisions in contracts about handling, security, disposal
- **PROPORTIONALITY** — reflect the principles (minimisation, storage limits, accuracy)
- **DOCUMENTATION** — maintain records of processing activities

**Part II:**
- **TRAINING** — communicate rules and limitations to employees
- **SECURITY** — document measures and their adequacy (physical access controls, passwords, network security, staff training, backups)
- **INCIDENT HANDLING** — train employees on reporting; identify the responsible person for DPA notification
- **CONTACT POINT** — publicize a contact for data subject rights requests

---

## Transfers to Non-EU Countries (Chapter V, Articles 44–50)

- **Within EU:** principle of free movement of personal data applies
- **Outside EU:** requires adequate level of protection; mechanisms:
  - **EC adequacy decision** (e.g., EU–US Data Privacy Framework)
  - **Standard contractual clauses** on data protection
  - Binding corporate rules / Approved codes of conduct / Certification mechanisms / Appropriate safeguards
  - Explicit consent or necessity in specific situations (Art. 49)

---

## Claims, Enforcement, and Fines

**Data subject remedies (Chapter VIII, Art. 77–82):**
- Lodge complaint with **supervisory authority** (Czech: ÚOOÚ — Office of Personal Data Protection)
- Judicial protection — court of data subject's place of residence
- **Direct damages claims** — joint and several liability; **strict liability** (liberation only if controller/processor proves no fault)

**Administrative fines (Art. 83–84):**
- Must be effective, proportionate, and dissuasive
- Factors: nature/gravity/duration of breach, intention or negligence, efforts to mitigate damage
- Fine is usually **preceded by a corrective measure** with a deadline
- CZ largest fine: **Avast (April 2024) — €13.7 million** — failed to anonymize browsing data of >100 million users before sharing with third parties for market analysis

---

## Privacy Techniques and Selected Issues

### Data Aggregation

- **Data aggregation** = merging or compiling data from multiple datasets to enable processing not possible before the merge
- Combination of data at a certain sensitivity level can yield **information at a higher sensitivity level**
- Example: age + insurance change + specialist visit + GP change → inference of serious illness
- Privacy legislation is strongly against large-scale aggregation

### Inference

- **Inference** = obtaining information of higher sensitivity by processing information at a lower sensitivity level
- Or: indirect access to information without direct access to the data representing it
- Example: querying a statistical database with very specific criteria can isolate a single individual

### Statistical Databases

- Contain sensitive information about individuals but are intended only for **statistical queries** (population-level analysis)
- Query results must **not** provide information about particular individuals
- Famous case: **Dorothy Denning** revealing her boss' salary by crafting a series of aggregate queries
- **Database compromise types:**
  - **Positive compromise** — query series yields exactly 1 record
  - **Partial compromise** — result is 0 or 1, revealing information about an entity even without the precise value

---

## Computer Ethics

### Definitions

- **Ethics** = area of moral philosophy studying concepts of right and wrong behavior; proposes systems of moral principles and rules of conduct
- **Computer ethics** = application of moral values and rules for work with computers
  - Changes in technology → changes in society and values
  - Aim: identify urgent questions and formulate answers about moral background of individual actions and responsibilities when working with computers

### Motivating Behavior (Don Parker's 3 Powers)

1. **Power of law and regulations** — fear of punishment, rule of force
2. **Power of conventions** — professional codes, codes of conduct (voluntary or semi-voluntary)
3. **Moral settings** — customs, experience of social circles

### Two Practical Ethical Questions

1. If I do something to somebody, **would I mind if somebody else did this to me** or someone close to me?
2. When considering an action, how about **asking first those whom this action will impact** (or their representatives)?

---

## ACM Code of Ethics and Professional Conduct

**ACM** = Association for Computing Machinery — the most influential professional association in computing.

**1. General Ethical Principles:**
- 1.1 Contribute to society and human well-being
- 1.2 Avoid harm
- 1.3 Be honest and trustworthy
- 1.4 Be fair and do not discriminate
- 1.5 Respect intellectual property
- 1.6 Respect privacy
- 1.7 Honor confidentiality

**2. Professional Responsibilities:**
- 2.1 Strive for high quality in processes and products
- 2.2 Maintain high standards of competence, conduct, and ethical practice
- 2.3 Know and respect existing professional rules
- 2.4 Accept and provide appropriate professional review
- 2.5 Give comprehensive evaluations including risk analysis
- 2.6 Perform work only in areas of competence
- 2.7 Foster public awareness of computing and its consequences
- 2.8 Access resources only when authorized or compelled by the public good
- 2.9 Design systems that are **robustly and usably secure**

**3. Professional Leadership Principles:**
- 3.1 Ensure the public good is the central concern
- 3.2 Encourage social responsibilities within organizations
- 3.3 Manage personnel and resources to enhance quality of working life
- 3.4 Support policies reflecting the Code's principles
- 3.5 Create opportunities for professional growth
- 3.6 Use care when modifying or retiring systems
- 3.7 Take special care of systems integrated into society's infrastructure

---

## Developers and GDPR (2021 Study by Alhazmi & Arachchilage)

- Study: 22 developers, multiple countries; tested what developers struggle with most when implementing GDPR
- **Key findings:**
  - Developers did not know good techniques for: limiting data storage time, limiting purposes, ensuring data accuracy
  - Difficulty differentiating between GDPR principles (especially purpose limitation vs. fair/lawful/transparent processing)
  - Even European developers familiar with GDPR had difficulty with specific implementation
  - Developers focused mainly on functional requirements — data integrity and confidentiality — not privacy principles
  - Lacked resources and guidance, especially for retention periods and purpose limitations
  - Privacy implementation depends strongly on **organizational culture and support**

---

---

## Lecture 8 – Computer Security: Introduction

> Lecture notes: [[lectures/L08-ComputerSecurity-Intro|L08]] | Related concepts: [[concepts/Risk-Assessment|Risk Assessment]] · [[concepts/IDS-IPS|IDS/IPS]]

### Terminology

| | Definition |
|---|---|
| **Computer security** | Combined art, science and engineering practice of protecting computer-related assets from unauthorized actions and their consequences, either by preventing such actions or detecting and then recovering from them |
| **Information security** | Aims to protect information/data — covers also non-computer information, but does NOT cover computer assets unrelated to data |
| **Cybersecurity** | Practice of protecting broader-defined systems from unauthorized actions and their consequences |
| **Cyberspace** | Widespread digital technology-interconnected space (term from science fiction, Gibson, 1980s) |

**Computer security** scope in this course: protecting **data, hardware, software, communication networks, and physical-world devices they control** from **intentional misuse by unauthorized parties** (= access/control by entities other than legitimate owners/authorized agents).

Not in scope: unintentional damage, reliability/redundancy failures (may be used to support security but are not studied).

---

### Six High-Level Computer Security Goals

End-goals (delivered as properties/services); supporting mechanisms shown separately.

#### 1. Confidentiality
- Non-public information remains accessible **only to authorized parties**, whether at rest or in transit
- Supported by **access control** (OS/file system) and **data encryption** (secret key allows recovery)
- Can also be provided by procedural means (physical access control to storage media)

#### 2. Integrity
- Data, software, or hardware remains **unaltered except by authorized parties**
- Supported by access controls and cryptographic algorithms (combat malicious violations)
- Error detection/correction codes address benign hardware errors
- Note: integrity of *people* (resisting bribery, blackmail, coercion) is related and important

#### 3. Authorization
- Computing resources accessible **only by authorized entities** (approved by owner or domain administrator)
- Achieved through **access control mechanisms** restricting access to physical devices, services, data
- **Principals** = agents representing users, communicating entities, or system processes
- A principal has associated **privileges** specifying authorized resources; identity claims must be verified

#### 4. Availability
- Information, services, and resources remain **accessible for authorized use**
- Focus: protection from intentional deletion/disruption, including **denial of service (DoS)** attacks overwhelming resources

#### 5. Authentication
- Assurance that a principal, data, or software is **genuine** relative to expectations
- **Entity authentication** = assurance that identity of a principal in a transaction is as asserted → supports authorization
- **Data origin authentication** = assurance that the source of data/software is as asserted → implies data integrity
  - Data modification by an unauthorized entity changes the effective source
- Authentication supports **attribution** (indicating to whom an action is ascribed) → thus supports **accountability**

#### 6. Accountability
- Ability to **identify principals responsible for past actions**
- In digital world: transaction evidence/logs recording principal identifiers such that principals cannot later **repudiate** commitments/actions
- Merriam-Webster: "an obligation or willingness to accept responsibility or to account for one's actions"
- Conventional evidence (paper records, logs) supports accountability in the physical world

**Diagram (Fig. 1.1):** Access control → confidentiality, integrity, authorization, availability; entity authentication + data origin authentication underpin access control; authentication + digital evidence → accountability.

---

### Trusted vs. Trustworthy

| | Meaning |
|---|---|
| **Trusted** | Has our confidence — whether deserved or not; relied on to meet expectations; if it fails, all guarantees are lost |
| **Trustworthy** | *Deserves* our confidence; will reliably meet expectations |
| **Trusted ≠ Trustworthy** | A bank that went bankrupt after 10 years was *trusted*, but was not *trustworthy* |

- Trusted systems typically underwent **security certification** based on security evaluation
- Scope and depth of evaluations can vary significantly
- **Trust** has dual meaning: (1) belief a system satisfies security requirements; (2) the chance a system can breach the security policy **without leaving any trace of evidence** — appreciating this caveat is critical

---

### Elementary Taxonomy

| Term | Definition |
|---|---|
| **Asset** | Information, software, hardware, computing/communications services — what we wish to protect |
| **Vulnerability** | Weakness: design flaws, implementation flaws, deployment/configuration issues (e.g., default passwords, debugging interfaces left enabled) — "a hole in the system" |
| **Security policy** | Specification of design intent: what is and is not allowed; specifies assets, authorized users/means, services, controls |
| **Violation** | System moved into an unauthorized (non-secure) state |
| **Attack** | Deliberate execution of one or more steps intended to cause a security violation; attacks exploit vulnerabilities |
| **Threat** | Combination of circumstances and entities that might harm assets |
| **Adversary** | Source/threat agent behind a potential attack |
| **Attacker** | Adversary once a threat is activated into an actual attack |
| **Attack vector** | Specific method or sequence of steps by which attacks are carried out |
| **Countermeasures** | Provided to **prevent** violations |
| **Controls** | Provided to **detect** violations in order to react and limit damage |
| **Security mechanisms** | Technical means of enforcement: specialized devices, software techniques, algorithms, protocols |

**Security policy and states:**
- Formal policy defines every system state as either *authorized (secure)* or *unauthorized (non-secure)*
- System starts in secure state; actions cause state transitions; violation = entry into non-secure state
- In practice, policies are often informal guidelines; value under-appreciated until incidents occur
- **No security policy = no security** (envisioned/requested)

**House example (policy illustration):** Policy: no one enters without a family member; only family may remove objects. Unlocked back door = vulnerability. Stranger entering through it and stealing TV = attack. Attack vector = unlocked door. Threat = individual motivated to profit by stealing.

**Countermeasures and controls** support and enforce security policies; include operational/management processes, OS enforcement by software monitors, access control, and security mechanisms.

---

### Adversary (Attacker) Model

**Five adversary attributes to consider:**
1. **Objectives** — suggest target assets requiring special protection
2. **Methods** — anticipated attack techniques/types
3. **Capabilities** — computing resources (CPU, storage, bandwidth), skills, knowledge, personnel, opportunity (e.g., physical access)
4. **Funding level** — influences determination, methods, and capabilities
5. **Outsider vs. insider** — outsider has no prior special access; insider typically has knowledge advantage and lower-level authorization

**No attacker model = no real need of security** (you don't know what to defend against).

#### Adversary Classes (hardware security world, extended)

| Class | Name | Key Characteristics |
|---|---|---|
| **Class 0** | Script kiddies | No system knowledge; exploit existing tools; trial-and-error |
| **Class 1** | Clever outsiders | Often intelligent; insufficient system knowledge; moderately sophisticated equipment; exploit existing weaknesses |
| **Class 1.5** | Well-equipped outsiders | Basic system knowledge; low-cost equipment for new attacks; university labs |
| **Class 2** | Knowledgeable insiders | Specialized education + experience; understand most parts of the system; access to sophisticated analysis tools |
| **Class 3** | Well-funded organizations | Teams of specialists with complementary skills; in-depth analysis; most sophisticated tools; design new attacks |

---

### Classical Attacker Models

#### Needham & Schroeder (1978)
- Attacker can **eavesdrop and interfere with all communication** → record, modify, replay, inject messages
- Node internal processes are **safe** (secret keys, encryption processes safe inside nodes)
- Paper: *Using Encryption for Authentication in Large Networks of Computers*, ACM Communications

#### Dolev & Yao (1983)
- Network = set of abstract machines exchanging messages (formal terms); terms reveal some but not all internal structure
- Adversary can **overhear, intercept, and synthesize any message**, limited only by cryptographic constraints
- Sometimes stated as: **"the attacker carries the message"**
- Paper: *On the Security of Public Key Protocols*, IEEE Trans. on Information Theory

---

### Security Evaluation vs. Penetration Testing

| | Formal Security Evaluation | Penetration Testing |
|---|---|---|
| **Who** | Third-party lab | Hired consultants or customers |
| **Result** | Verified conformance with evaluation criteria per standards | Demonstrated exploits on live systems |
| **Cost/time** | Considerable | Lower |
| **Recertification** | Required for even smallest changes | Not mandatory |
| **Typical clients** | Government, banks, critical infrastructure | Any organization |
| **Notes** | — | Bug bounty programs are part of this; black/white/gray-box variants |

---

### Security Analysis vs. Vulnerability Assessment

**Security analysis:**
- Primary aim: identify **design vulnerabilities** and overlooked threats
- Ideally begins early in lifecycle and continues through design + implementation
- Techniques: manual source code review, security review of design documents — uncovers vulnerabilities not visible via black-box testing
- Secondary aim: suggest defenses when weaknesses found
- Considers architecture, deployment environment, threat model

**Vulnerability assessment:**
- Identifying weaknesses in **deployed** systems
- Subset of security analysis; itself includes pen testing

---

### Threat Model

A threat model **identifies threats, threat agents, and attack vectors** that the target system considers **in scope** to defend against.

- In scope = known from the past or anticipated; out-of-scope threats must be **explicitly recorded**
- Takes into account adversary modeling; should identify all **assumptions** about system, environment, and attackers
- Ideally built from both practical experience and analytical reasoning
- Must be **continually adapted** to inventive attackers exploiting rapidly evolving technology

#### STRIDE Threat Modeling

Keyword-based approach to stimulate structured threat thinking:

| Letter | Threat | Description |
|---|---|---|
| **S** | Spoofing | Impersonating a thing (e.g., website) or entity (e.g., user) |
| **T** | Tampering | Unauthorized altering of code, stored data, transmitted packets |
| **R** | Repudiation | Denying responsibility for past actions |
| **I** | Information disclosure | Unauthorized release of data |
| **D** | Denial of service | Impairing availability/quality of services via resource consumption or induced errors |
| **E** | Escalation of privilege | Gaining higher access via vulnerabilities; malware using a foothold to extend privileges |

#### Threat Model Quality
- Quality = how accurately the model reflects the real system and environment
- **Mismatch between model and reality → dangerously false sense of security**
- Details are important in security — high-level abstractions often miss critical details
- Two major modeling errors:
  1. **Invalid assumptions** (often including misplaced trust)
  2. **Focus on the wrong threats**

---

### 3 + 1 Key Questions for Security Analysis

1. What **assets** are valuable? (What are your protection goals?)
2. What **potential attacks** put them at risk?
3. How can potentially damaging actions be **stopped or managed**?
4. **What is your threat model?**

---

---

## Lecture 9 – Security Policy, Risk Assessment, Business Security Operations

> Lecture notes: [[lectures/L09-SecurityPolicy-Risk|L09]] | Related concepts: [[concepts/Risk-Assessment|Risk Assessment]]

### Steps in Security (Cyclic Process)

1. **Risk assessment** execution
2. **Security policy** definition
3. **Security architecture** design
4. **Security mechanisms** – design and implementation
5. **Monitoring** of security-related events
- → Loopback to step 1 (continuous cycle)

---

### Security Policy

**What a security policy should specify:**
- Assets requiring protection
- Specific users allowed to access specific assets, and the allowed means of access
- Security services to be provided
- System controls that must be in place

**Purpose:**
- Aims to **minimize (manage) risks** → risk analysis is therefore crucial
- Presents **high-level strategies** on how to achieve aims using security services and controls
- Should clearly state what and how security services and controls should achieve
- Usually written in **standard human language** (with recommended vocabulary/structure); may use formalism for simple systems

**Policy levels:**

| Level | Name | Characteristics | Typical lifetime |
|---|---|---|---|
| High | Company (IT) security policy | Abstracts from specific IT deployed; specifies core assets, basic security infrastructure, responsibilities | 5+ years |
| Lower | System security policy | Recognizes concrete core IT components; more detailed; sometimes split into separate documents | 2–3 years |

#### Bell-LaPadula Confidentiality Policy (Model)
- State machine-based **multilevel security policy**, originally for military applications (US Air Force, 1973)
- Uses **security labels** on objects and **clearances** for subjects
- Levels (simplified): **Top Secret > Secret > Confidential > Public**
- Two core rules:
  1. **Simple security property** (ss-property): subject at a given level may **not read up** (NRU) — cannot read objects at a higher level
  2. ***-property** (star-property): subject at a given level may **not write down** (NWD) — cannot write to objects at a lower level

**CAPP A.PEER example (Common Criteria OS certification):**
- Assumes all communicating systems are under the same management control and the same security policy
- Applicable only if the entire network resides within a single management domain
- Does not address trusting external systems — meaning in practice: system disconnected from external networks, disabled media drives
- Protection level appropriate for a non-hostile, well-managed user community; **not** intended against determined well-funded attackers or malicious administrators

---

### Risk and Risk Assessment

**Risk** = expected loss due to harmful future events
- Relative to the identified set of assets and over a **fixed time period**
- Depends on: threat agents, probability of attack (and its success, which requires vulnerabilities), and expected losses

**Risk formula:**

**R = T · V · C** where:
- **T** = threat information (probability that particular threats are instantiated by attackers in a given period)
- **V** = existence of vulnerabilities
- **C** = cost/impact of a successful attack (asset value)

Equivalently: **R = P · C** where **P = T · V** (probability that a threat agent takes an action that successfully exploits a vulnerability). Intangible costs (e.g., corporate reputation) are included in C.

**Principal risk assessment questions:**
1. What assets are most valuable, and what are their values?
2. What system vulnerabilities exist?
3. What are the relevant threat agents and attack vectors?
4. What are the associated estimates of attack probabilities or frequencies?

**Consequence of wrong risk assessment:** wrong controls and wrong security measures → assets improperly protected, inefficiently protected, or not protected at all.

---

### Approaches to Risk Assessment

| Approach | Method | Notes |
|---|---|---|
| **Quantitative** | Compute numerical estimates of risk exposure (typically in $) | Good only for incidents that occur regularly with large historical data samples; poor for rare/novel attacks |
| **Qualitative** | Qualitative ratings and comparative reasoning; typically 3–5 levels | Used in practical risk assessments |

#### Annual Loss Expectancy (ALE) — Quantitative

**ALE = Σ Fᵢ · Cᵢ** (summed over all event types i)

- **Fᵢ** = estimated annualized frequency of events of type i (combining threat probability and vulnerability exploitability)
- **Cᵢ** = average loss per occurrence of event type i
- Purpose: answers "How much is it worth investing in protecting this asset?"

**Drawbacks of quantitative risk assessment:**
- Estimates often based on outdated observations; solid rich historical data needed
- Incomplete knowledge of vulnerabilities (worsened by rapid technology evolution)
- Difficulty quantifying **intangible assets** (strategic information, reputation)
- Incomplete knowledge of threat agents and their adversary classes
- Human attacker behavior cannot be accurately predicted; motivation and capabilities evolve
- **For unlikely events, ALE analysis is a guessing exercise with little evidence supporting its use in practice**

#### Qualitative Risk Assessment — Steps

1. **Specify assets** to be considered (some controls may imply unacceptable costs or new risks)
2. **Consider foreseeable threats** — collect evidence via interviews, review system settings and records (requires knowledge and experience)
3. **Review existing controls** (safeguards) and their appropriateness
4. **Estimate likelihood** that each threat would succeed, and the **impact** it would create
5. **Risk score = probability score/level · impact score/level**

#### Risk Rating Matrix (Qualitative)

Risk levels 1–5 (V.LOW to V.HIGH):

| C (cost/impact) | P = V.LOW | P = LOW | P = MODERATE | P = HIGH | P = V.HIGH |
|---|---|---|---|---|---|
| V.LOW (negligible) | 1 | 1 | 1 | 1 | 1 |
| LOW (limited) | 1 | 2 | 2 | 2 | 2 |
| MODERATE (serious) | 1 | 2 | 3 | 3 | 3 |
| HIGH (severe/catastrophic) | 2 | 2 | 3 | 4 | 4 |
| V.HIGH (multiply catastrophic) | 2 | 3 | 4 | 5 | 5 |

#### CIS RAM — Multi-Dimensional Impact
- Some methodologies (e.g., **CIS RAM** — Center for Internet Security Risk Assessment Method) view impact in two dimensions: **impact on own institution** AND **impact on others**
- The **higher** of the two impact levels is used in calculations
- A safeguard is "reasonable" if its risk score (burden) is lower than the observed risk score

#### Risk Assessment — Final Notes
- **Desired state** = where risks to defined assets would not cause harm bigger than what the organization is **willing to accept/tolerate**
- Risk assessment will hardly ever assess *all* foreseeable risks → must be **repeated iteratively**
- As part of risk management, assessment should lead to risks being **mitigated** (exceptionally eliminated), **prioritized**, and **monitored**
- Reference frameworks: **NIST SP 800-37** (Risk Management Framework), **CIS RAM**

---

### ISO/IEC 27000 — Information Security Management System (ISMS)

- Family of information security standards focused on **security management**
- Known for **ISMS = Information Security Management System**
- Originated from industry (Shell, UK banks) → inspired British Standard BS7799
- Currently a large set of several dozen standards and drafts (not all regularly reviewed)

#### PDCA Cycle (Plan–Do–Check–Act)

| Phase | Action |
|---|---|
| **Plan** | Establish ISMS policy, objectives, processes and procedures relevant to managing risk and improving information security |
| **Do** | Implement and operate the ISMS policy, controls, processes, and procedures |
| **Check** | Assess and measure process performance against ISMS policy, objectives, and practical experience; report to management |
| **Act** | Take corrective and preventive actions based on audit results and management review; achieve continual improvement |

#### Nine Steps to Success (ISMS Implementation)
1. Assemble an implementation team
2. Develop the implementation plan
3. Initiate the ISMS
4. Define the ISMS scope
5. Identify your security baseline
6. Establish a risk management process
7. Implement a risk treatment plan
8. Measure, monitor and review
9. Certify your ISMS

---

### Security Checks Overview

| Question | Answer |
|---|---|
| What and where will we focus? | Risk assessment |
| How and where will security be delivered? | Security policy |
| What system suits our needs? | Security evaluation / evaluation criteria (+ did they actually build it in?) |
| Do we do security correctly? | Internal (security) audit |
| What do *they* do wrong? | External audit, pen testing, etc. |

---

### IT (Security) Audit

**Five phases:**
1. **Audit planning**
2. **Documentation and checks** of controls/safeguards — emphasis on documentation, not technologies
3. **Compliance tests** — answers "Is the documentation correct?"
4. **Special tests** — real tests of functionality in selected areas
5. **Overall assessment** of the system

**Internal audit** must be performed by a department **independent of the IT division**.

---

### Business Continuity Planning (BCP)

**BCP** deals with preventive and reactive (recovery) actions to enable an organization's activities before and during disaster recovery.

- Goal: keep all **essential functions** of the organization running when critical events arise (earthquake, floods, pandemic, strikes, etc.)
- Important to clarify scope: IT operations only, or the **whole organization**?

**Disaster recovery** = practice aiming to recover or maintain ongoing operations of vital systems and technology infrastructures after a serious disruptive event.

#### Key Disaster Recovery Metrics

| Metric | Definition |
|---|---|
| **RPO** (Recovery Point Objective) | Maximum target period where data might be **permanently lost** from an IT service due to a major incident; shorter RPO demands better equipment and backup connections |
| **RTO** (Recovery Time Objective) | Target time and service level at which a service must be **restored after a disaster** to avoid unacceptable consequences of business continuity break |

- Both RPO and RTO are **service-specific**; targets are not met if they are exceeded
- Some scenarios require a **secondary site** when the primary site cannot be recovered

---

---

# Lecture 10 – Network Attacks, Intrusion Detection, and Vulnerability Assessment

> Lecture notes: [[lectures/L10-NetworkAttacks-IDS|L10]] | Related concepts: [[concepts/IDS-IPS|IDS/IPS]] · [[concepts/Risk-Assessment|Risk Assessment]]

## Network Monitoring

### Active vs. Passive Network Monitoring

| | **Active Monitoring** | **Passive Monitoring** |
|---|---|---|
| **How it works** | Sends network probes into the network | Observes traffic at an observation point |
| **Pros** | Detailed information; various monitoring methods | Transparent; does not increase link utilization |
| **Cons** | Inserts traffic into network; monitored object is aware | Limited info from data; encrypted traffic issues |
| **Tools** | nmap, Shodan, Internet Census 2012 | Wireshark (tshark), tcpdump, Zeek |

---

### Packet Capture – HUBs, Switches, TAPs, SPANs

**Network devices and sniffing:**
- **Hub** – broadcasts all Ethernet frames to every LAN host → easy to capture all traffic
- **Switch** – sends frames only to the MAC address destination → isolates traffic
- **Promiscuous mode** – NIC captures all frames passing the interface (not just addressed to it)

**Capture methods in switched networks:**
- **Mirroring method (SPAN)** – switch port mirroring; switch copies traffic to analyzer port
- **In-line method** – analyzer placed physically in-line (network TAP or hub inserted between devices)

---

### Nmap – Port Scanning

**Nmap capabilities:**
- **Host discovery** (ping scanning) – discovers which targets on the network are online
- **Port scanning** (TCP SYN "stealth" scan) – sends probes and classifies remote ports:
  - **open** – port accepts connections (SYN-ACK received)
  - **closed** – port reachable but no service (RST received)
  - **filtered** – no response (firewall blocks)
- **OS and application fingerprinting** – identifies remote OS, version, and services on open ports (`nmap -O`)

---

## Intrusion Detection

### Basic Terms

| Term | Definition |
|---|---|
| **Intrusion / Incident** | Event on a host or network that **violates security policy** or threatens to put a system in an unauthorized state |
| **Intrusion Detection** | Process of **passive monitoring and analysing events** to identify and report intrusions |
| **IDS** (Intrusion Detection System) | Automates event analysis to **report events** requiring human attention |
| **IPS** (Intrusion Prevention System) | Automates **real-time active responses** to mitigate and stop in-progress attacks |

### IDS Architecture

**Host-based IDS:**
- Events derived from **logs** (application, kernel), audit records
- Data must be **collected centrally** to provide views beyond a single host

**Network-based IDS:**
- Events derived from **packets** at a strategic vantage point
- Provides **network-wide views** (network situational awareness)

---

### Intrusion Detection Problem – False Positives and Negatives

|  | **Intrusion exists** | **No intrusion** |
|---|---|---|
| **Alarm raised** | **True Positive (TP)** – intrusion detected ✓ | **False Positive (FP)** – false alarm |
| **No alarm raised** | **False Negative (FN)** – intrusion missed ✗ | **True Negative (TN)** – normal operation ✓ |

- **FN (missed intrusion)** = most dangerous outcome
- **FP (false alarm)** = wastes analyst time, causes alert fatigue
- Goal: evaluate **detection precision** of a detection method — the criterion (threshold) value shifts the balance between FP and FN

---

### Intrusion Detection Methods

| IDS Approach | Alarm when... | Key properties |
|---|---|---|
| **Signature-based** (expert defines malicious patterns) | Events match known-bad patterns | Fast, accurate (fewer FPs); detects **only known attacks** |
| **Specification-based** (expert defines allowed actions) | Events deviate from per-application specifications of legitimate actions | Can detect **new attacks**; no alarm on newly seen allowed event; specs are protocol/program-specific |
| **Anomaly-based** (learning-based profile of normal) | Events deviate from profiles of normal | Can detect **new attacks**; needs training period; higher FP rate (abnormal may be benign) |

- There is **no one-size-fits-all** approach to intrusion detection
- Deployment and configuration of IDS is **tricky**; detection reliability may change over time

---

## Vulnerability Assessment

### Basic Terms

- **Vulnerability** = a weakness that can be exploited by an attacker
- **Vulnerability assessment** = process of **identifying, quantifying, and prioritizing** vulnerabilities in a system

**Three categories of tools:**

| Tool type | Purpose |
|---|---|
| **Reconnaissance tools** | Network mapping (scanning) |
| **Vulnerability scanners** | Automated vulnerability scanning |
| **Penetration testing tools** | Exploitation and security testing |

---

### Network Mapping (Scanning) – Reconnaissance

- **Network-based reconnaissance** is a common **precursor to attack** and contributes to Internet background noise
- **Port scanning** – identifies open ports; tools: **nmap** (active), p0f (passive)
  - Port states: **open**, **closed**, **blocked (filtered)**
- **OS and application fingerprinting** – identifies remote OS and version; for each open port identifies service and version

---

### Vulnerability Scanning – CVE

**Common Vulnerabilities and Exposures (CVE):**
- A **publicly known list** of information-security vulnerabilities
- Each entry: **ID number**, **description**, public reference
- CVE examples:
  - **Shellshock** – CVE-2014-6271
  - **Log4Shell** – CVE-2021-44228
  - **Heartbleed** – CVE-2014-0160

**Vulnerability scanners:**
- **Automate scanning** hosts from outside to find vulnerabilities and insecure configurations
- Scanned hosts (services) are in **operating state** during scan
- Results categorized by **CVSS severity**: Low, Medium, High, Critical

**CVE growth (cumulative since 1988):**
- ~260 000 total vulnerabilities; ~32% have weaponized exploits; ~3% are zero days
- Trend: accelerating growth, especially after 2015

---

### Penetration Testing

- **Penetration testing** = gaining assurance in system security by **attempting to breach** some or all of its security
- Uses **same tools and techniques** as an actual adversary
- **MITRE ATT&CK** = knowledge base of adversary **tactics and techniques** based on real-world observations (attack.mitre.org)
  - Covers: Reconnaissance, Resource Development, Initial Access, Execution, Persistence, Privilege Escalation, Defense Evasion, Credential Access, Discovery, Lateral Movement, Collection, C&C, Exfiltration, Impact

**Key tools:**
- **Metasploit** – vulnerability exploitation framework
- **Kali Linux** – Linux distribution for digital forensics and penetration testing

---

## Network-Based Attacks

### DDoS – Distributed Denial of Service

**Definition:** Overwhelms a server or network with **excessive traffic from many sources** to disrupt normal operations and make services unavailable.

**Attack types:**

| Type | Example |
|---|---|
| **Volume-based** | UDP floods |
| **Protocol attacks** | SYN floods |
| **Application-layer** | HTTP floods |

**Attack variants:**
- **Direct flooding** – botnet machines directly flood the victim
- **Reflection DDoS** – attacker spoofs victim's IP → reflectors (e.g., open DNS servers) flood the victim with responses

**SYN Flooding with Spoofed IP:**
1. Attacker sends SYN with **forged source IPs** (y₁, y₂, y₃…)
2. Victim replies SYN-ACK to those spoofed addresses
3. Victim's connection table fills up → **legitimate connections rejected**
4. Secondary victims also affected (receive unsolicited SYN-ACKs)

**Impact:** Service downtime, lost revenue, reputation damage, high recovery costs

**Mitigation:**
- **Ingress filtering** – filters packets *entering* a network; blocks packets with spoofed source IPs
- **Egress filtering** – filters packets *leaving* a network; prevents spoofed packets from exiting
- Both mitigate IP spoofing → reduce TCP SYN, UDP, and ICMP flooding attacks
- Firewalls, traffic limiting/filtering, DDoS protection services

---

### ARP Spoofing (Address Resolution Attack)

**Context:** ARP maps **IPv4 addresses to MAC addresses** on a local network.

**Attack steps:**
1. Attacker performs **MAC flooding** → forces switch to behave like a hub (broadcasts all frames)
2. Attacker sends **fake ARP replies** → poisons ARP caches of victim and/or gateway
3. Victim's ARP cache: `<IP_gateway, MAC_attacker>` → traffic intended for gateway goes to attacker

**Goal:** Associate attacker's MAC address with the IP of another host (like the gateway) → **Man-in-the-Middle**

**Enables:**
- Data interception (sniffing)
- Session hijacking
- Man-in-the-Middle attacks

**Mitigation:**
- Use **static ARP entries**
- **Switch port security** (Dynamic ARP Inspection)

---

---

# Lecture 12 – Programming Secure Software

> Lecture notes: [[lectures/L12-SecureProgramming|L12]] | Related concepts: [[concepts/AES-DES|AES & DES]] · [[concepts/Hash-Functions|Hash Functions]]

## Core Mantra

> **"Security features ≠ Secure features"** (Howard & LeBlanc, 2002)  
> Writing security features is only ~10% of the work. The remaining 90% is ensuring all non-security code is also secure.

> **"Reliable software does what it is supposed to do. Secure software does what it is supposed to do, and nothing else."** — Ivan Arce

---

## What Software Security Means

- **Good development + security practices**: education, testing, defence in depth, code review
- **Safety vs. Security**: Safety deals with random errors (CRC sufficient); Security deals with intentional attackers (who can recompute CRC after malicious change)
- **Secure Development Lifecycle (SDL)**: security is a process, not a product
- **Systematic deployment & maintenance**: monitor, triage, fix, update — including 3rd-party libraries
- **Usability**: easy to use correctly, hard to misuse (for both developers and end users)
- **Automated + manual review and testing**: CI, pentesting, security code review, AI-assisted reviews
- **Language-specific issues**: buffer overflow (C/C++), code injection (Java), etc.
- **Secure crypto primitives**: libraries, RNG, password handling, secure channels

---

## Cost of Insecure Software

| Perspective | Cost |
|-------------|------|
| Vendor | Fixing post-release is more expensive (testing, announcements, updating, risk of new bugs) |
| Liability | GDPR settlements, reputation loss |
| Vulnerability market | Up to **$2.5M** for a single zero-click Android exploit (Zerodium, 2025) |
| Defense | Cost of defense is **decreasing** (better training, tools) but software complexity is increasing |

---

## Attacker Goals & Vulnerability Taxonomy

### What attackers use bugs for:
1. **Steal data** (keys in memory, file contents)
2. **Bypass protection** (access rights, authentication, session hijack)
3. **Execute malicious code** (custom payload, ROP)
4. **Cause Denial of Service** (resource exhaustion, infinite loop, regex)
5. Real attacks **chain multiple steps** (DoS → null pointer → payload → exfiltration)

### Threat Modeling — Microsoft STRIDE:

| Letter | Threat |
|--------|--------|
| **S** | Spoofing |
| **T** | Tampering |
| **R** | Repudiation |
| **I** | Information disclosure |
| **D** | Denial of service |
| **E** | Elevation of privilege |

---

## Where to Find Bug Information

| Resource | Purpose |
|----------|---------|
| **CWE** (cwe.mitre.org) | Taxonomy of weakness categories — systematic classification |
| **CVE** (cve.mitre.org) | Database of real discovered vulnerabilities (US NIST) |
| **EVD** (euvd.enisa.europa.eu) | European Vulnerability Database (EU equivalent) |
| **CISA KEV** | List of actively exploited vulnerabilities |
| **CWE Top 25** | Most dangerous weaknesses (general) |
| **OWASP Top 10** | Most critical web application security risks |
| **OWASP LLM Top 10** | Top risks for LLM/AI applications |
| **Google Zero-Day list** | Actively exploited undisclosed vulnerabilities in the wild |

> **Acronym map:** CWE = taxonomy; CVE/EUVD = ID systems; NVD/EVD = databases; CVSS/EPSS/KEV = prioritization; OWASP = domain-specific risk lists

---

## Vulnerability Disclosure

| Term | Definition |
|------|-----------|
| **Bug** | Unintended/unwanted behavior (e.g., buffer overflow) |
| **Vulnerability** | Bug accessible to an attacker on a system with value |
| **Proof of Concept (PoC)** | Tool that triggers the bug (e.g., crashes the program) |
| **Exploit** | Tool that triggers bug AND executes payload (e.g., creates root account) |
| **0-day** | Exploit of an unpatched vulnerability unknown to the vendor |
| **Responsible disclosure** | Notifying vendor privately before public release; often with a deadline |
| **Bug bounty** | Vendors pay researchers for reported vulnerabilities |
| **Whitehat / Blackhat** | Ethical / malicious hackers |
| **Red team / Blue team** | Attackers / Defenders |

**Zero-day market**: Up to millions per exploit, paid while bug remains undiscovered (vendor not notified!).

---

## 5 Layers of Protection Against Bugs

| Layer | Examples |
|-------|---------|
| **1. Source code level** | Memory-safe languages, input checking, sanitization, safe function alternatives |
| **2. Extensive testing** | SAST, dynamic analysis, fuzzing, code review |
| **3. Compiler** | Stack canary, runtime checks (`-fstack-protector-all`) |
| **4. Execution environment** | DEP, ASLR, sandboxing, hardware isolation |
| **5. Defence in depth** | All above combined in SDL; multiple layers |

### Microsoft's Secure Development Lifecycle (SDL)
A systematic process integrating security at every phase: requirements → design → implementation → verification → release → response.

---

## Secure Languages & Libraries

- Newer languages (Go, Rust) offer **memory safety** by design
- Use platform standard APIs (e.g., AndroidKeyStore)
- Use **hard-to-misuse** libraries: `Libsodium crypto_secretbox_easy()` > OpenSSL > custom code
- **Monitor dependencies** for new vulnerabilities (e.g., GitHub Dependabot, xz backdoor example)
- **Never implement your own crypto** — it almost always repeats others' mistakes

### Safer Function Alternatives (C example):
```c
// UNSAFE — no bounds checking:
char *gets(char *buffer);

// SAFE — requires size parameter:
char *gets_s(char *buffer, size_t sizeInCharacters);
```
Follow **CERT C Coding Standard** for language-specific guidance.

---

## Compiler & Platform Hardening

| Attack | Protection Mechanism |
|--------|---------------------|
| Write shellcode to stack via buffer overflow, execute it | **DEP** (Data Execution Prevention) — non-executable bit on memory pages |
| Learn exact memory address, read/write from there | **ASLR** (Address Space Layout Randomization) — addresses randomized each run |
| Overwrite return address, jump to arbitrary function (ROP) | **Control Flow Integrity (CFI)** — allows only jumps defined in source-code call graph |
| Overwrite return address on stack | **Stack canary** — random value placed before return addr; checked before function return |

> **Stack canary bypass**: attacker can leak the canary value via another vulnerability (e.g., format string read), then overwrite it correctly.

---

## Static vs. Dynamic Analysis

| | **Static Analysis (SAST)** | **Dynamic Analysis** |
|-|---------------------------|----------------------|
| When runs | Without executing the program | On running program |
| Input needed | Source code or compiled binary | Must compile & run |
| When in dev | Even on unfinished code | Needs runnable code |
| False positives | Higher (conservative model) | Lower |
| False negatives | Higher (may miss runtime issues) | Lower (actually executes) |
| Example tools | Cppcheck, semgrep, Bandit, CodeQL, PREfast | Valgrind, AFL++, Clang sanitizers |

### False Positives vs. False Negatives

| | **False Positives** | **False Negatives** |
|-|---------------------|---------------------|
| Definition | Tool reports error that **isn't real** | Real error **NOT reported** by tool |
| Cause | Conservative analysis, inaccurate model | Missing detection rules |
| Problem | Developers ignore reports, extra work | Real bugs are missed |
| Who tolerates | Exploit researchers (accept many FP to find few real bugs) | Nobody — defeats the purpose |
| Who can't tolerate | Ordinary developers (will stop using tool) | Security-critical contexts |

### Key Tools:

**Cppcheck** (C/C++ static analysis):
- Open-source, cross-platform
- Plugin for IDEs, CI systems (GitHub Actions, Jenkins)

**Valgrind** (dynamic analysis suite):
- `memcheck` — memory leaks, dangling pointers, uninitialized variables, buffer overflows
- `massif` — heap profiler
- `helgrind` — concurrency issues
- `callgrind` — call graph generation

---

## Fuzzing

**Fuzzing** = generating random/semi-random inputs and feeding them to an application to find crashes or unexpected behavior.

```bash
# Simplest fuzzer (very limited):
cat /dev/random | ./target_app
```

### Fuzzing Process:
1. Investigate app input/output
2. Prepare data model (optional)
3. Generate fuzzed inputs
4. Send to application
5. Monitor for crashes/signals
6. Analyze logs and save crashing inputs

### Fuzzing Advantages & Disadvantages:

| Advantages | Disadvantages |
|-----------|--------------|
| Simple to start | Usually finds only low-hanging fruit (simpler bugs) |
| Finds bugs missed by humans | Hard to evaluate impact/severity |
| Works even without source code | Black-box testing limits understanding |
| Repeatable (crashing inputs saved) | Time-consuming |

**Key fuzzing tools**: AFL++, Radamsa, OWASP ZAP (for stateful protocols), Peach (grammar-based), Microsoft MiniFuzz

**Coverage is crucial** — only visited code paths are tested.

---

## Taint Analysis

Tracks **propagation of untrusted (tainted) values** through program:

- **Taint sources**: attacker-controlled inputs (files, stdin, network, keyboard)
- **Taint sinks**: sensitive operations (e.g., `system()`, SQL query, `html_output`)
- Every object receiving a tainted value becomes tainted
- **Alert fired** when tainted value reaches a sink without sanitization
- Native in Ruby, Perl; tools like **FindSecBugs** / **semgrep** add it to Java/Python

---

## Sanitizers

- **Code instrumentation** added at compile-time that detects issues at runtime
- Clang sanitizers: **AddressSanitizer**, **ThreadSanitizer**, **UndefinedBehaviorSanitizer**
- Dynamic → **code coverage is critical** (only reached code is checked)
- Can be **combined with fuzzers** to maximize coverage

---

## Continuous Integration (CI)

- Running analysis tools **manually is insufficient** for active development
- Integrate both **static** and **dynamic** analysis into CI pipeline:
  - Static analysis → runs even before commit (on unfinished code)
  - Dynamic analysis → needs tests for coverage; run overnight or continuously
- **GitHub Dependabot**: automatically alerts/fixes vulnerable dependencies
- CI workflow: Developer commits → webhook triggers → tests + static analysis → artifacts

---

## AI-Assisted Coding & Security

- Treat **AI-generated code as an external Pull Request** — review critically
- Treat **agentic tooling as a potentially malicious insider** — what prevents it from doing harm?
- Use harness + guardrails: tests, linters, access control, limited permissions
- AI tools for review: GitHub Copilot, Gemini Code Assist — helpful but **non-deterministic** and have **limited context** → risk of false confidence
- **OWASP GenAI Security Project** covers LLM-specific risks

---

## Security Code Review

| Type | Access |
|------|--------|
| **Whitebox** | Full source code available |
| **Greybox** | Partial code/documentation |
| **Blackbox** | Binary or API only |

### Review Phases:
1. **Architecture review** — design choices and design flaws
2. **Code review** — quality of implementation vs. architecture
3. **Deployment review** — how app is deployed, used, maintained

### Practical Hints:
- Build overall picture fast; use tools for low-hanging fruit
- Use LLM-assisted tooling (significant progress 2025/2026)
- Focus on most sensitive/problematic areas
- More reviewers = more issues found
- Be pragmatic, results-driven, creative

---

## Security Coding — Cryptography

**Typical issues found in crypto/security code:**
- **Hardcoded passwords** or keys in source code
- **Weak algorithms**: DES, MD5, ECB mode
- **Insufficient randomness**: `rand()`, low entropy after boot
- **Custom "encryption"**: XOR, security by obscurity
- **Key distribution/revocation** not handled
- **Certificate problems**: self-signed, expired, missing intermediate CA

**Rules:**
- Never design/implement your own cryptographic functions
- Use well-established, easy-to-use library primitives
- Use standards: **OAuth, OIDC, JWT**, etc.
- Ask for security review — "everyone can design what they cannot break"

---

## Typical Real-World Problems

| Problem | Details |
|---------|---------|
| **Developer education** | Mature developer wouldn't make most found bugs; training is expensive |
| **Legacy code** | Too many issues to fix; fixing can break things → reluctance |
| **Missing specification** | Implementation drifts; assumptions invalidated when code reused elsewhere |
| **"Functionality first"** | Security added only later — too late to fix architectural issues |
| **3rd-party dependencies** | No control over code; possibly unmaintained; rewriting is usually worse |
| **Human issues** | "No problems before we started looking"; hard to admit failures; unresponsive companies |
| **Security economics** | Those who pay for fix ≠ those who suffer; developer KPI = functionality, not security |
| **Customers don't update** | Fear of breaking changes; small releases help |
| **Improper new tech adoption** | JWT, JSON, protobuf misuse; new languages = new tooling to learn |

---

## Tools Summary

| Tool Type | Pros | Cons | Examples |
|-----------|------|------|---------|
| **Static analyzer** | Works on unfinished code | Higher false positives | Cppcheck, semgrep, CodeQL, PREfast |
| **Dynamic analyzer** | Low false positives | Must compile; needs test coverage | Valgrind, Clang sanitizers |
| **Fuzzer** | Easy to start, no test coverage needed | Finds simpler bugs; time-consuming | AFL++, Radamsa, OWASP ZAP |
| **Sanitizer** | Runtime detection | Performance overhead | AddressSanitizer, ThreadSanitizer |
| **Taint analysis** | Deep flow analysis | Complex setup; FP explosion risk | semgrep, FindSecBugs |

> **No single tool catches everything!** Tools don't understand overall architecture, application semantics, or social context. Use them **together**.

### Tool Selection Criteria:
- Suitability for target **language, OS, domain**
- **Maturity** and maintenance status
- **False positive rate** (critical for ordinary developers)
- **Performance** on benchmarks
- **CI integration** (GitHub Actions, Jenkins, etc.)
- Price and support

---

---

# Lecture 13 – Security Standards, Case Studies & Closing Notes

> Related concepts: [[concepts/Risk-Assessment|Risk Assessment]] · [[concepts/PKI-Certificates|PKI & Certificates]] · [[concepts/Authentication|Authentication]]

## Standardization in Security

**Benefits of standards (general + security-specific):**
- Compatibility of components, reduction of mistakes, interchangeability, reduced costs of systems and user training
- **Security-specific:** reduction of poor alternatives and outright mistakes — "re-inventing the wheel" is especially dangerous in security

> **Key fact:** Mere existence of a standard does not oblige anybody. Application must be specifically requested by: law/regulations, regulator/association requirement, or customer requirement.

**Standards already covered in PV080:** Common Criteria, ISO 27000, AES, DES, SHA-3, various protocols

---

## Standardization Organizations

| Organization | Scope |
|---|---|
| **ISO** (International Organization for Standardization) | International |
| **IEC** (International Electrotechnical Commission) | International (electrical/electronic) |
| **ITU** (International Telecommunication Union) | International (telecom) |
| **CEN** / **CENELEC** | European |
| **ETSI** (European Telecom Standards Institute) | European (telecom) |
| **ANSI, BS, DIN, ČSNI** | National (US, UK, Germany, CZ) |
| **IETF** (Internet Engineering Task Force) | Internet standards (RFCs) — open |

**ISO details:**
- Est. 1947, HQ Geneva, 165 countries
- Works with IEC on many security standards (ISO/IEC joint)
- **Rigid process:** 3+ years to develop a standard
- Standards valid for **5 years**, then must be reviewed and revised/reissued
- **Charges for standards** (unlike IETF/NIST)

---

## Cryptographic Algorithm Standards

- Cryptographic algorithms are **critical for most security systems**
- **National interests** historically significant: US boycotted standardization for decades → US standards (like DES) dominated
- Russia pushing algorithmically weak standards hoping US-haters will adopt them
- **Big surprise:** NIST chose a **non-US proposal for AES** (Rijndael — Belgian)

---

## NIST Standards

### FIPS (Federal Information Processing Standards Publications)

| Standard | Content |
|---|---|
| **FIPS 140** | Security Requirements for Cryptographic Modules |
| **FIPS 180-4** | Secure Hash Standard (SHS) = SHA-2 |
| **FIPS 186-4** | Digital Signature Standard (DSS) |
| **FIPS 198-1** | Keyed-Hash Message Authentication Code (HMAC) |
| **FIPS 197** | Advanced Encryption Standard (AES) |
| **FIPS 202** | SHA-3 |

### NIST Special Publications (SP 800-series)

| Publication | Content |
|---|---|
| SP 800-14 | Principles and Practices for Securing IT Systems |
| SP 800-30 | Guide for Conducting Risk Assessments |
| SP 800-39 | Managing Information Security Risk (Org, Mission, IS View) |
| SP 800-53 | Security and Privacy Controls for Information Systems and Organizations |
| SP 800-160 | Systems Security Engineering (multidisciplinary approach) |

---

## Security Evaluation Criteria — History

| Criteria | Origin | Notes |
|---|---|---|
| **Orange Book** (TCSEC) | USA, 1985 | Class D (no security) to A1 (highest, math. formalism) |
| **ITSEC** | Europe | Split functionality and assurance |
| **CTCPEC** | Canada | Functionality split: confidentiality, integrity, accountability, availability |
| **Federal Criteria** | USA, 1990s | Development halted |
| **Common Criteria (CC)** | Worldwide | Current standard; ISO/IEC 15408 |

**Motivation for creating evaluation criteria:**
- Minimize costs of government institutions when individually requesting/comparing/selecting secure computing systems
- Facilitate specification of design and development requirements

---

## Common Criteria (CC)

**Full name:** Common Criteria for Information Technology Security Evaluation  
**Version:** v3.1, release 5 (April 2017) | **Standard:** ISO/IEC 15408

**Serves three stakeholders:** users, manufacturers, evaluators

**Core process:**
- Evaluation requested (and **paid for**) by vendor/manufacturer with a **security claim**
- Claim is **verified** and either certified or debunked
- **Functionality** — what security functions does the TOE have?
- **Assurance** — grounds for confidence that it meets security objectives

### Key CC Concepts

| Term | Meaning |
|---|---|
| **TOE** (Target of Evaluation) | What is being evaluated (product/system) |
| **TSF** (TOE Security Functions) | HW, SW, FW used by the TOE |
| **TSC** (TSF Scope of Control) | Interactions under the TOE security policy |
| **PP** (Protection Profile) | Catalogued self-standing evaluation document (e.g., for smartcards, biometrics) |
| **ST** (Security Target) | Theoretical concept/aim — the claimed security properties |
| **SFR** (Security Functional Requirement) | Individual security functions provided by the TOE |

### CC Evaluation Assurance Levels (EAL)

- **7 levels: EAL1 (lowest) < EAL7 (highest)**
- Hierarchical: higher levels include lower ones + new components
- Two catalogues: one for **functionality**, one for **assurance**

### CC Evaluation Steps (in a nutshell)
1. Define the product/system for evaluation
2. Specify its functionality
3. Specify the assurance level claimed
4. See details of evaluation with a certification body
5. Prepare evidence

### Challenges of Product Security Certifications
- **Lack of transparency**
- **Expensive**
- **Slow**

---

## FIPS 140-2 / 140-3

- **Purpose:** Cryptographic modules validation (algorithm, modules, entropy certificates)
- **Scope:** NIST (US) & Canada, now worldwide
- **5000+ certificates** total; 950–1000 active
- **Validates:** limited set of approved algorithms and parameters (note: MD5 is NOT approved)
- FIPS 140-2 certificates were set to expire; 140-3 is the current version

**FIPS 140 Security Requirements include:**
- Physical security
- Module interfaces
- OS security
- Services and authentication
- Key management
- Methods for mitigating other attacks

---

## Case Study #1: DoTheThing! s.r.o. (Growing IT Infrastructure)

**Scenario:** Staffing company growing from 2 owners to a larger organization with an internal database, VPN, full-time admin/developer, and negotiations for merger with GetItThere a.s.

### Security Stages & Issues

| Growth stage | Security approach | Key problems |
|---|---|---|
| Initial (2 owners, 1 laptop) | Both can do anything; manual malware checks, manual backups | No separation of duties |
| Small company (added HR, acct) | Part-time admin; firewall + AV installed | AV not controlling all communications |
| Growing (added intra DB, VPN) | Full-time admin/dev; biometrics for auth; shared-secret VPN | VPN bypassed via shared password; automated external connections |

### The Incident: Database Corruption
- Last working backup was **22 days old**
- Automated backup SW had **firewall connection denials** — nobody noticed
- 5 people were logged into `intra` at time of corruption
- External salespeople's laptops had **automatic connections** to internal network (kids played games, triggered connections)
- Logs on `intra` machine **did not exist**; firewall logs were **not monitored**

### Technical Mistakes Made

- **Antivirus:** installed on individual machines but NOT controlling all files/communications company-wide
- **Firewall:** bypassed by shared password + automated connections from external machines
- **Authentication:** done individually at system nodes, **not synchronized**
- **Backup:** never checked; nobody knew RPO & RTO; SW failure went undetected for 22 days
- **Logs:** not analyzed; sometimes did not exist at all

### Root Cause (Wrong Approach)

- **"All can do anything"** principle — works only in extremely small groups
- **"Somebody else surely takes care of that"** — **never works**
- Individual components were carefully selected, but the **overall construction** was not providing expected security
- Security components were **not periodically reviewed** or updated
- No defined security policy → impossible to build a secure, scalable system

### Correct Steps in Security (Loop)
1. Risk assessment execution
2. Security policy definition
3. Security architecture design
4. Security mechanisms — design and implementation
5. **Monitoring** of security-related events
6. → **Loop back to step 1**

> **IT risks are just risks:** solving them is not only about technology — requires clear responsibility, systematic approach, repeated assessments, and winning users over.

---

## Case Study #2: Honan's "Epic Hacking" (Social Engineering Chain)

**Source:** Wired, 2012

**Attack chain:**
1. Attacker called Amazon → used partial credit card number (last 4 digits: 4513) + billing address to **add a new card**
2. Called Apple → used the newly added Amazon card + billing address to **reset Apple ID** (impersonation)
3. With Apple ID access → **remotely wiped** Apple devices (iPhone, iPad, MacBook)
4. With @mat handle and linked Gmail → **reset Gmail** and **Twitter** via daisy-chained account recovery

**Key lesson:** Each step used **publicly available or easily obtainable information** to unlock the next step — no technical hacking required.

---

## Case Study #3: Phishing — Enablers and Defenses

### URL Spoofing & Typosquatting

- HTML anchor: `<a href="http://evil.com">good.com</a>` — displayed text ≠ actual link
- **Typosquatting:** `paypal.com` vs. `paypal-security.com` vs. `paypaI.com` (capital I instead of l) vs. `pay.pal.com`
- Many users **do not check URL bars**

### Phishing & Spear Phishing

| Type | Definition |
|---|---|
| **Phishing** | Tricks user into visiting fraudulent version of a legitimate site (via email link, search result, redirect) |
| **Spear phishing** | **Targeted** phishing at specific user or group with a common denominator |

**Statistics:**
- **80%** of reported security incidents are phishing-related
- Phishing causes a loss of **$17,700 per minute**

### Why Phishing Works

- Users have **incomplete mental models** of how the internet and HTTPS work
- Many don't distinguish **browser chrome vs. website content**
- Certificate/security information is **hard to find** for users
- Security indicators are **rarely clicked**
- Yet: ~**70% of users** now adhere to SSL (HTTPS) warnings, though comprehension rates are low

### Phishing Defenses

**Technical:**
- Spam filtering
- Domain blacklisting
- Awareness of **transient phishing sites** (set up briefly, not yet blacklisted)

**Training & education:**
- Train users to detect phishing attempts
- Tell them where to **report** phishing
- Keep training material **up-to-date** (security indicators change)

---

## Key Terminology

| Term | Definition |
|---|---|
| **Social engineering** | Tricking a user into taking some action |
| **Identity theft** | Taking over a victim's real-world identity (e.g., acquiring credit cards in their name); legal responsibility falls to the victim |
| **Mental model** | User's view (correct or otherwise) of how a system works and consequences of actions — crucial for security UX design |
| **Typosquatting** | Registering domain names that are common typos or visual lookalikes of legitimate domains |

> **Mental models matter:** Users' actions are directed by their mental models. Designers must understand user models when developing security mechanisms. (Volkamer & Renaud, 2013)

---

## Standards — Summary

- Standards enable a basic **"qualitative" level** of similar/compatible systems and support interoperability
- Standards **concentrate elementary knowledge** on the subject
- Standards can be used as a **measure for comparison/evaluation**
- **Plenty of standards exist** — choose the one that suits the need, rather than re-inventing the wheel
- **AI-generated code = external PR** — always review critically
