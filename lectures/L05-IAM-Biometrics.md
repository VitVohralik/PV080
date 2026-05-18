---
tags: [pv080, lecture, iam, biometrics, authentication, identity]
lecture: 5
---

# L05 – Identity and Access Management

> Full notes: [[Fulltext|Fulltext]]

## Identity

**Identity:** any subset of attributes associated with an individual that **uniquely identifies** them within a given set.
- An individual has **multiple identities** depending on context (max identities per person = unlimited)
- A **partial identity** is associated with a given context or role

## Authentication Factors

| Factor | Examples |
|--------|---------|
| **Something we know** | Passwords, PINs, passphrases |
| **Something we have** | Hardware token, smart card, cell phone |
| **Something we are** | Biometrics (fingerprint, face, iris) |
| **Somewhere we are** | Geolocation, proximity |

**MFA:** combination from **at least two** different categories.  
**SMS is NOT strong 2FA** — SIM swapping, SS7 attacks; deprecated by NIST SP 800-63B.

## Passwords

### Password Attacks

| Attack | Prevention |
|--------|-----------|
| **Online guessing** | Account lockout |
| **Offline guessing** | Salted + hashed storage |
| **Password capture** | MFA; password manager |
| **Credential stuffing** | Unique passwords per service |
| **Recovery mechanism attack** | Stronger recovery |

### Password Storage

Passwords stored **salted and hashed** — NOT encrypted.  
- Hash is one-way; compromise requires cracking per-password
- Salting prevents rainbow table attacks

### Password Managers

**Benefits:** unique password per service, high-complexity random passwords.  
**New risk:** single point of failure (weak master password = all passwords exposed).

### Best Practice

- Unique password per service; strong passphrase for email (master key to all accounts)
- Use MFA on critical services; use password manager

## Biometrics

See [[../concepts/Biometrics|Biometrics]] for full details.

| Error Metric | Definition |
|-------------|-----------|
| **FAR** | Impostor accepted (threshold too lenient) |
| **FRR** | Legitimate user rejected (threshold too strict) |
| **FTE** | Failed enrollment |
| **EER** | Point where FAR = FRR — comparison metric |

**If unauthorized users are "greater evil" → lower FAR → raise threshold → more FRR.**

**Biometrics are NOT secrets** — combine with other factors.  
Remote biometric auth is **two-staged**: user-to-device (biometric) + device-to-service (cryptographic).

### Fingerprint Sensor Types

| Type | Vulnerability |
|------|--------------|
| **Optical** | Easily fooled by fake fingers |
| **Capacitive** | More resistant |
| **Ultrasonic** | Most resistant (used in-display) |

### Face Recognition Attacks

- **CV Dazzle:** make-up patterns defeating face detection
- **Adversarial glasses (CMU 2016):** printed glasses fool DNNs with >90% success

## Hardware Tokens & OTP

OTP (One-Time Password) — valid for single use; prevents replay attacks.

| Method | Status |
|--------|--------|
| **SMS** | Not recommended (SS7 vulnerabilities) |
| **Hardware token** (RSA SecurID) | Strong |
| **Authenticator app** | Phone-based, convenient |
| **FIDO2 / YubiKey** | Phishing-resistant, strongest |

## Authorization & Access Control

| Model | Description |
|-------|-------------|
| **RBAC** | User → role → (object + operations) |
| **MAC** | System administrator-defined rules — users cannot override |
| **DAC** | Resource owner-defined rules — more flexible |

**Default-deny:** unless explicitly permitted, access is not allowed.  
**Least privilege:** grant minimal access necessary.

## SSO & Federated Identity

**SAML 2.0:** XML-based SSO standard (SP, IdP, assertions).  
**SP-initiated flow:** User requests resource → SP redirects to IdP → User authenticates → IdP sends SAML assertion → SP validates → access granted.

**OAuth 2.0:** delegated authorization (access tokens, not credentials); published 2012.

**Other protocols/systems:**

| System | Notes |
|--------|-------|
| **LDAP** | Directory service |
| **Kerberos v5** | Auth used inside Active Directory |
| **OpenID Connect** | Identity layer on top of OAuth2 |
| **FreeIPA** | RedHat open-source IdM: Kerberos + LDAP + DNS + PKI |

## NIST SP 800-63 — Digital Identity Guidelines

| Level | Measures |
|-------|---------|
| **IAL** | Confidence in claimed identity |
| **AAL** | Strength of authentication process |
| **FAL** | Strength of federation assertions |

## Key Points

- **4 authentication factors:** know / have / are / somewhere we are; MFA = at least 2 different
- **SMS is NOT strong 2FA** — use hardware token, authenticator app, or FIDO2
- **Server password storage:** salted hash — one-way prevents bulk recovery
- **Biometrics are not secrets** — combine with other factors
- **EER** = equal error rate — used for comparing biometric systems
- **SAML 2.0** = XML-based SSO; **OAuth2** = delegated authorization

## Related

- [[../concepts/Biometrics|Biometrics]]
- [[../concepts/Authentication|Authentication]]
- [[../concepts/PKI-Certificates|PKI & Certificates]]
- [[L04-TLS-SSH-PostQuantum|L04 – TLS, SSH, Post-Quantum]]
- [[L06-Privacy|L06 – Privacy]]
