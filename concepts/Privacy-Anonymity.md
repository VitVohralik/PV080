---
tags: [pv080, concept, privacy, anonymity]
---

# Privacy & Anonymity

> Related lectures: [[../lectures/L06-Privacy|L06]] | [[../lectures/L07-GDPR-Ethics|L07]]

## 4 Privacy Functionalities (Pfitzmann & Hansen)

| # | Functionality | Definition |
|---|--------------|-----------|
| 1 | **Anonymity** | System supports use of resources/services without revealing user identity |
| 2 | **Pseudonymity** | System supports use without revealing identity, yet with accountability (identity can be found out) |
| 3 | **Unlinkability** | User may make multiple uses without others being able to link uses together |
| 4 | **Unobservability** | User may use a resource without others (especially third parties) being able to observe that the resource is being used |

**Key relation:** With respect to the same attacker, **unobservability ⇒ anonymity**.

## Anonymity — Details

- **Extremely rare** in practice — requires complete system isolation
- **Anonymity set:** the set of all possible subjects who might cause an action
- Bigger anonymity set ≠ necessarily better anonymity quality
- In practice: only partial/conditional anonymity

## Pseudonymity — Details

- Very practical; covers entire spectrum from accountability to anonymity
- Ongoing use of same pseudonym allows establishing a reputation

### Pseudonym Hierarchy (most linkable → most anonymous)

```
person pseudonym
    ├── role pseudonym
    └── relationship pseudonym
              └── role-relationship pseudonym
                          └── transaction pseudonym (most anonymous)
```

| Pseudonym Type | Scope | Linkability |
|---------------|-------|------------|
| **Person pseudonym** | Across all contexts | Highest |
| **Role pseudonym** | Tied to a role (e.g., "admin") | High |
| **Relationship pseudonym** | Specific relationship | Medium-high |
| **Transaction pseudonym** | Single-use, per transaction | Lowest |

**Public pseudonym** = identifier that is intentionally public (can be known to others).  
**Originally non-public pseudonym** = was not public initially, but may be disclosed in some cases.

**Digital pseudonym:** bit string that is (1) unique as identifier and (2) suitable for authenticating holder's IOIs.

## Unlinkability — Details

- Does not consider user identity — focuses on **scope of usage** by same user
- IOIs (Items of Interest): subjects, messages, actions — attacker cannot sufficiently determine if IOIs are related

## Unobservability — Details

- Protected values are about *usage of resources/services*, not data about users
- Methods: traffic analysis protection, mix networks

## Common Criteria (CC) Privacy Components (FPR Family)

| CC Component | Key requirement |
|-------------|-----------------|
| **FPR_ANO** Anonymity (2 levels) | L1: others cannot determine identity; L2: TSF does not ask for identity |
| **FPR_PSE** Pseudonymity (3 levels) | L1: accountability maintained; L2: reversible (TSF can recover identity); L3: alias construction rules |
| **FPR_UNL** Unlinkability (1 level) | Users/subjects cannot determine whether same user caused certain operations |
| **FPR_UNO** Unobservability (4 levels) | L1: cannot determine if operation performed; L2: avoid concentration; L3: no solicitation; L4: authorised user can observe |

CC approach: existential view — property either IS or IS NOT present; evaluation is discrete (Y/N).

## Mix Networks (Chaum 1981)

- Core technique for anonymity via dummy messages + non-optimal routing
- Defined by David Chaum in 1981
- Attacker may be outsider (tapping lines) or insider (controlling some stations)

## Onion Routing / TOR

**Onion Routing:** messages wrapped in multiple encryption layers — each router decrypts one layer.  
**TOR:** implementation of onion routing with additional features:
- **Directory servers** — maintain list of onion routers
- **Hidden services** — services accessible only via TOR (.onion addresses)
- **Integrity protection** — defends against tagging attacks (modifying ciphertext to track it)

**TOR provides real-time (interactive) anonymity** unlike Mixminion (email-based, stores-and-forwards).

**Attack on TOR anonymity:** If attacker controls both entry and exit nodes, timing correlation can de-anonymize users.

**Project AN.ON** provides similar services to TOR (anonymization network).  
**Eternity server** — services immune to censorship: content stored distributed, impossible to remove.

## Traffic Analysis vs Mix Networks

Traffic analysis: observe when and to/from whom messages are sent (even if content is encrypted).  
Mix networks defeat traffic analysis by batching, reordering, and injecting dummy messages.

## Related

- [[GDPR|GDPR]]
- [[../lectures/L06-Privacy|L06 – Privacy]]
- [[../lectures/L07-GDPR-Ethics|L07 – GDPR & Ethics]]
