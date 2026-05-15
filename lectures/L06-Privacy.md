---
tags: [pv080, lecture, privacy, anonymity]
lecture: 6
---

# L06 – Privacy Introduction

> Full notes: [[../fulltext|fulltext]]

## What is Privacy?

- **Privacy:** ability to seclude oneself or information, expressing oneself selectively
- **Information privacy:** concerned with control of personal data; no exact definition due to subjectivity

**Personal data:** any information relating to an identified or identifiable natural person. Whether data is personal depends on **context** (same data can sometimes identify a person and sometimes not).

## 4 Privacy Functionalities (Pfitzmann & Hansen)

See [[../concepts/Privacy-Anonymity|Privacy & Anonymity]] for full details.

| # | Functionality | Definition |
|---|--------------|-----------|
| 1 | **Anonymity** | Use resources without revealing identity |
| 2 | **Pseudonymity** | Use resources without revealing identity, but with accountability |
| 3 | **Unlinkability** | Multiple uses cannot be linked together |
| 4 | **Unobservability** | Even the fact of using a resource is hidden |

**Key:** Unobservability ⇒ anonymity (w.r.t. same attacker).

## Anonymity

- **Extremely rare** in practice — requires complete system isolation
- **Anonymity set:** set of all possible subjects who might cause an action
- Bigger anonymity set ≠ better quality
- In practice: only partial/conditional anonymity

## Pseudonymity

Covers entire spectrum from accountability (fully linkable) to anonymity (fully unlinkable).

**Pseudonym types (most linkable → most anonymous):**

```
person pseudonym → role pseudonym → relationship pseudonym
    → role-relationship pseudonym → transaction pseudonym
```

- Ongoing use of same pseudonym allows establishing reputation
- **Digital pseudonym:** bit string that is (1) unique as identifier and (2) suitable for authenticating IOIs

## Unlinkability

- Does not consider identity — focuses on **scope of usage** by same user
- IOIs (Items of Interest): attacker cannot determine if IOIs are related

## Unobservability

- Protected values are about *usage of resources/services* — not data about users
- Methods of protection against traffic analysis

## Common Criteria Privacy Components

| CC Component | Description |
|-------------|-------------|
| **FPR_ANO** Anonymity (2 levels) | L1: cannot determine user identity; L2: TSF doesn't ask for identity |
| **FPR_PSE** Pseudonymity (3 levels) | L1: accountability maintained; L2: reversible; L3: alias construction |
| **FPR_UNL** Unlinkability (1 level) | Cannot determine if same user caused operations |
| **FPR_UNO** Unobservability (4 levels) | L1: cannot determine if operation occurred; L2-L4: increasing protection |

CC approach: existential view — property either IS or IS NOT present (Y/N).

## Mix Networks (Chaum 1981)

- Achieves anonymity/unlinkability via: **dummy messages + non-optimal routing**
- Defined by David Chaum, 1981
- Attacker may be outsider (tapping lines) or insider (controlling some stations)

## Onion Routing / TOR

- **Onion Routing:** layered encryption; each router decrypts one layer
- **TOR additions vs. basic OR:** directory servers, hidden services (.onion), integrity checking (defends against tagging attacks)
- TOR provides **real-time interactive anonymity** (unlike Mixminion which is store-and-forward)
- **Attack on TOR:** if attacker controls entry + exit nodes, timing correlation can de-anonymize

## Anonymizing Remailers

- **Remailer:** server that strips email headers and forwards to recipient
- **Mixminion:** advanced anonymous email system (store-and-forward)
- **AN.ON / JonDonym:** anonymization network providing similar services to TOR

**Eternity server:** distributed system making services immune to censorship.

## Value of Privacy Studies

- **FIDIS location tracking study:** people accept ~€43 for 1-month tracking; gender difference (men bid 1.4–1.8× higher)
- **Email tracking study:** median ~€30–50 for 2-week tracking; ~10% dropped out per escalating scenario
- **Dark web prices (Czech Republic 2017):** physical address + phone + email ≈ 0.15 CZK; national ID number ≈ 5.17 CZK
- Privacy has relatively low monetary value for most people in practice

## Key Points

- **4 privacy functionalities:** anonymity, pseudonymity, unlinkability, unobservability
- **True anonymity** is extremely rare — full isolation required
- **Pseudonymity** = practical middle ground: hidden identity but accountability preserved
- **Transaction pseudonym** = most anonymous type; person pseudonym = most linkable
- **Mix networks** = core technique for anonymity (dummy traffic + non-optimal routing)
- **CC privacy components:** FPR_ANO, FPR_PSE, FPR_UNL, FPR_UNO — evaluate existence (Y/N)

## Related

- [[../concepts/Privacy-Anonymity|Privacy & Anonymity]]
- [[../concepts/GDPR|GDPR]]
- [[L07-GDPR-Ethics|L07 – GDPR & Ethics]]
