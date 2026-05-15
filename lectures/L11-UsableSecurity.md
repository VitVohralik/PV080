---
tags: [pv080, lecture, usable-security, hci, ux]
lecture: 11
---

# L11 – Usable Security

> Full notes: [[../fulltext|fulltext]]

## Motivational Example: Hawaii Missile Alert (2018)

Employee sent real ballistic missile threat alert to all Hawaii residents via SMS. Took **38 minutes** to cancel.

**Cause:** Bad warning system UI — real alerts mixed with drill options in dropdown, no confirmation dialog, difficult to undo.

**Lesson:** Bad usability in security systems has real-world consequences. Don't blame the user — improve the system.

## Usability (Jakob Nielsen, 1994)

**Usefulness = Utility + Usability**

| Dimension | Question |
|-----------|----------|
| **Learnability** | How easy to succeed the first time? |
| **Efficiency** | How quickly can learned users perform tasks? |
| **Memorability** | How easily can users re-establish proficiency? |
| **Errors** | How many errors? How severe? How easy to recover? |
| **Satisfaction** | How pleasant is it to use? |

**Security is NOT a primary goal for users** — nobody says "I'm going to do some security now!" Security should be like a **display case**: protect without obstructing use.

## Usable Security Definition

> **Usable security:** multidisciplinary field investigating and improving system security/privacy **focusing on human interaction with the system**.

- Uses methods of HCI, psychology, sociology, design
- **Core philosophy: Don't blame the user, improve the system!**

## Usable Security for Regular Users

### Password Authentication

| Rule | Attack prevented | Caveat |
|------|-----------------|--------|
| Long | Brute-force | — |
| Unpredictable | Dictionary attack | — |
| No reuse | Credential stuffing | OK for low-priority accounts |
| Don't write down | Physical theft | **BUT:** use password manager instead |
| Change regularly | Long-term compromise | **BUT:** leads to predictable patterns |

**Passphrases (xkcd 936):**
- `Tr0ub4dor&3` — ~28 bits entropy, hard to remember ❌
- `correct horse battery staple` — ~44 bits entropy, easy to remember ✅

**Users' mental models about passwords:** brute force understood; dictionary attacks often NOT in model; mass automated attacks not understood; "!" at end considered sufficient; leet speak considered secure.

**System improvements over training:**
- Single-sign-on (fewer authentications)
- Auth wallets (centralized to device)
- Biometrics (nothing to remember)
- Passkeys (user-friendly asymmetric crypto)

### Data Integrity Fingerprint Representation

| Generation | Format |
|------------|--------|
| **Hex fingerprint** | Raw hex in groups of 4 |
| **PGP words** | Hex mapped to pronounceable words |
| **ASCII image** | Geometric visual (SSH key) |
| **Robohash** | Hash → unique robot/avatar image |

### Habituation

**Habituation** = diminishing response to frequently repeated stimulus (ignoring repeated things).

**Problem:** warnings get ignored; changes can happen unnoticed.  
**Solution:** avoid unnecessary use; avoid designs that lead to habituation; use jargon sparingly.  
**Good design:** show notification only once per new permission (not every time); require waiting before clicking.

## Usable Security for IT Professionals

**Impact Pyramid:**
```
OS developers → Library developers → Software developers → Admins → End users
(few, HIGHEST impact per person)                                   (most people, lower impact)
```

**Usability even more critical for IT professionals** — one developer's mistake affects millions.

### SSH CLI Analysis

**Host key changed warning** is much more alarming than first-connection warning — appropriately so.  
**PuTTY vs SSH CLI:** PuTTY gives clearer options (Yes / No / Cancel vs. yes/no).

### Security API Pitfalls (PayPal SDK 2012)

```php
curl_setopt($ch, CURL_SSL_VERIFYPEER, TRUE)
curl_setopt($ch, CURL_SSL_VERIFYHOST, TRUE)  // ← STILL WRONG!
```

`CURL_SSL_VERIFYHOST` is **Int** (not Bool!): TRUE evaluates as 1 = debug mode (not actual verification).  
Correct: `CURL_SSL_VERIFYHOST = 2`

**Lesson: Have secure defaults.** The default should be the most secure option.

## Five Principles of Usable Security Design

1. **You (designers) are not your users** — preferences differ; user testing is crucial
2. **Prefer system usability over user training** — improve system rather than training users
3. **Security is a process, not a state** — everything evolves; status quo = becoming insecure
4. **Make context-aware decisions** — decide whether to show a warning at all; may be better to refuse than warn
5. **Have secure defaults** — the default way should be the most secure way

## Application Examples

### MUNI IS Login
- Login redirects to **different domain** (islogin.cz) — security reason: prevents password autofill attacks, smaller admin surface
- Usability concern: violates "check the domain" guideline for users

### IS MU vs MUNI SSO 2FA
- Two separate 2FA systems: different options, different usability
- Root cause: historical development by different teams, organizational politics
- **Illustrates:** Security is a process, not a state; organizational context matters

### Keybase Sign-Out Screen
- Asks "Do you know your password?" before signing out
- Blue/focused button = "Test password" (reflects what developer wants, not user)
- Better UX: focused button should reflect user's most likely intent

## Key Points

- **Bad usability = real security incidents** (Hawaii missile alert)
- **Five principles:** you ≠ users; system > training; process not state; context-aware; secure defaults
- **Habituation** — repeated warnings get ignored; be judicious about when to show them
- **Mental models matter** — designers must understand how users think about attacks
- **Secure defaults** — insecure options should require explicit effort (not the default)
- PayPal SDK lesson: API type mismatches (Bool vs Int) can undermine security

## Related

- [[../concepts/Authentication|Authentication]]
- [[L05-IAM-Biometrics|L05 – IAM & Biometrics]]
- [[L12-SecureProgramming|L12 – Secure Programming]]
