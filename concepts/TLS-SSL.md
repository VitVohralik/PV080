---
tags: [pv080, concept, protocols, tls, ssl]
---

# TLS / SSL

> Related lectures: [[../lectures/L04-TLS-SSH-PostQuantum|L04]]

## Overview

TLS (Transport Layer Security) secures HTTPS, email (SMTP/POP3), VPN, VoIP, etc.  
Position: **above TCP, below application layer** (HTTP, SMTP, etc.)  
Author of SSL: Taher ElGamal (Netscape) — "father of SSL"

## Version History

| Version | Year | Status |
|---------|------|--------|
| SSL 2.0 | 1995 | Deprecated 2011 |
| SSL 3.0 | 1996 | Deprecated 2015 |
| TLS 1.0 | 1999 | First IETF standard |
| TLS 1.3 | 2018 | **Current standard** |

## Protocol Structure

**Two layers:**
- **Handshake layer** — security parameter setup at session start; key exchange + server auth
- **Record layer** — protects application data using parameters from handshake

## What TLS Provides

| Property | Mechanism |
|----------|---------|
| **Confidentiality** | Encryption (AES-GCM or ChaCha20) |
| **Integrity** | AEAD (TLS 1.3); MAC (older versions) |
| **Entity authentication** | Default: **unilateral (server only)**; mutual is rare |

**TLS does NOT provide non-repudiation** — integrity uses symmetric MAC (both sides know key).  
**TLS does NOT use digital signatures for application data** — signatures used only in handshake auth and certificates.

## TLS 1.3 Handshake (Simplified)

```
1. ClientHello → (offered versions, algorithm list, client nonce, key share)
2. ServerHello ← (server nonce, key share, selected options, server cert + signature, Finished MAC)
3. ClientAgain → (client cert if requested, client Finished MAC)
   ──────────────────────────────────────────────────────
   Application Data (AEAD-secured, both directions)
```

## Key Exchange Options in TLS 1.3

| Method | Description |
|--------|-----------|
| **DHE** (ephemeral DH) | FFDHE or ECDHE → provides **forward secrecy** |
| **PSK** (pre-shared key) | Long-term secret; no forward secrecy alone |
| **PSK + DHE** | Combines both — forward secrecy with PSK optimization |

## Record Layer Ciphers

- **AES-GCM** / **AES-CCM** — AEAD-integrated
- **ChaCha20-Poly1305** — stream cipher + Poly1305 MAC

## Session Resumption

After successful handshake, server sends `new_session_ticket`.  
Client includes ticket in next `ClientHello` → skip full handshake (expensive asymmetric ops).

## TLS Attacks

| Attack | Description | Example |
|--------|-------------|---------|
| **Implementation bug** | Flaws expose private keys or data | **Heartbleed** (OpenSSL) — read server memory |
| **Downgrade** | Force weaker algorithms | **Logjam** (512-bit DH); **DROWN** (SSLv2 fallback) |
| **Padding oracle** | Different responses to padding errors | **Lucky Thirteen** |
| **TLS interception** | Rogue cert, MITM | ISP/corporate proxy |

## Common Myths

1. **"TLS provides non-repudiation"** — FALSE (symmetric MAC = both parties know key)
2. **"TLS uses digital signatures for data"** — FALSE (only for handshake auth and certs)

## Related

- [[PKI-Certificates|PKI & Certificates]]
- [[AES-DES|AES & DES]]
- [[Authentication|Authentication]]
- [[../lectures/L04-TLS-SSH-PostQuantum|L04 – TLS, SSH, Post-Quantum]]
