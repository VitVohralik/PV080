---
tags: [pv080, concept, cryptography, signatures, authentication]
---

# Digital Signatures

> Related lectures: [[../lectures/L02-Hash-MAC-PublicKey|L02]] | [[../lectures/L03-PKI-eIDAS-Protocols|L03]]

## What Digital Signatures Provide

**Integrity + Data Origin Authentication + Non-Repudiation**  
**NOT confidentiality** — signing does not encrypt the message.

| Property | MAC | Digital Signature |
|----------|-----|-----------------|
| Key type | Symmetric (shared) | Asymmetric (private key) |
| Non-repudiation | ❌ (both parties know key) | ✅ (only signer has private key) |
| Verification | Anyone with secret key | Anyone with public key |

## Signing Process

```
1. Compute hash: h = H(message)
2. Sign hash: sig = Sign(h, private_key)     ← uses private key
3. Send: message ∥ sig
```

Sign the **hash**, not the full message (asymmetric operations on large data are too slow).

## Verification Process

```
1. Compute hash: h' = H(message)
2. Verify: Verify(sig, h', public_key)        ← uses public key
3. If h' matches recovered hash → signature valid
```

## Common Algorithms

| Algorithm | Basis |
|-----------|-------|
| **RSA-PSS** | Integer factoring |
| **DSA / ECDSA** | Discrete logarithm / Elliptic curves |
| **EdDSA (Ed25519)** | Edwards-curve; current recommendation |

**Minimum RSA key size: 2048 bits; recommended: 3072+ bits**

## PGP Digital Signature

In PGP: hash of message is signed with **sender's private key** (not recipient's key).  
Verification: recipient uses sender's public key to check the signature.

## eIDAS Signature Types

| Type | Legal strength |
|------|---------------|
| **(Simple) Electronic Signature** | Lowest — any electronic data used to sign |
| **Advanced Electronic Signature** | Based on a certificate (any cert) |
| **Qualified Electronic Signature (QES)** | Qualified cert + qualified device; **= handwritten signature legally** |

QES is cross-border recognized in all EU member states (Art. 25 eIDAS).

## Signing with Key Recovery vs. Without

- **Signature with message recovery (appendix):** hash is signed; verifier recomputes hash independently
- **Signature giving message recovery:** message can be recovered from the signature alone

## Related

- [[Hash-Functions|Hash Functions]]
- [[PKI-Certificates|PKI & Certificates]]
- [[../lectures/L02-Hash-MAC-PublicKey|L02 – Hash, MAC, Public Key]]
- [[../lectures/L03-PKI-eIDAS-Protocols|L03 – PKI, eIDAS, Protocols]]
