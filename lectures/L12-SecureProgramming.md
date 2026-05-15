---
tags: [pv080, lecture, secure-programming, ssdlc]
lecture: 12
---

# L12 – Programming Secure Software

> Full notes: [[../fulltext|fulltext]]

## Core Mantras

> **"Security features ≠ Secure features"** — Writing security features is only ~10% of the work.

> **"Reliable software does what it is supposed to do. Secure software does what it is supposed to do, and nothing else."** — Ivan Arce

## Vulnerability Taxonomy

| Term | Definition |
|------|-----------|
| **Bug** | Unintended/unwanted behavior |
| **Vulnerability** | Bug accessible to an attacker on a system with value |
| **PoC (Proof of Concept)** | Tool that triggers the bug (e.g., crashes program) |
| **Exploit** | Tool that triggers bug AND executes payload |
| **0-day** | Exploit of unpatched vulnerability unknown to vendor |
| **Responsible disclosure** | Notifying vendor privately before public release |
| **Bug bounty** | Vendors pay researchers for reported vulnerabilities |

**Zero-day market:** up to **$2.5M** for a single zero-click Android exploit (Zerodium, 2025).

## What Attackers Use Bugs For

1. Steal data (keys in memory, file contents)
2. Bypass protection (access rights, authentication, session hijack)
3. Execute malicious code (payload, ROP)
4. Cause Denial of Service (resource exhaustion, infinite loop)
5. Real attacks **chain multiple steps** (DoS → null pointer → payload → exfiltration)

## Bug Information Resources

| Resource | Purpose |
|----------|---------|
| **CWE** (cwe.mitre.org) | Taxonomy of weakness categories |
| **CVE** (cve.mitre.org) | Database of real discovered vulnerabilities |
| **CISA KEV** | List of actively exploited vulnerabilities |
| **CWE Top 25** | Most dangerous weaknesses |
| **OWASP Top 10** | Most critical web application security risks |

## 5 Layers of Protection

| Layer | Examples |
|-------|---------|
| **1. Source code** | Memory-safe languages, input checking, safe function alternatives |
| **2. Extensive testing** | SAST, dynamic analysis, fuzzing, code review |
| **3. Compiler** | Stack canary, runtime checks (`-fstack-protector-all`) |
| **4. Execution environment** | DEP, ASLR, sandboxing, hardware isolation |
| **5. Defence in depth** | All layers combined in SDL |

### Platform Protections

| Attack | Protection |
|--------|-----------|
| Execute shellcode from stack | **DEP** (Data Execution Prevention) — non-executable bit |
| Read/write from exact address | **ASLR** (Address Space Layout Randomization) — randomizes addresses |
| ROP (Return Oriented Programming) | **CFI** (Control Flow Integrity) — restricts valid jump targets |
| Overwrite return address | **Stack canary** — random value checked before function return |

**Stack canary bypass:** attacker leaks canary value via another vulnerability (e.g., format string read).

## Secure Languages & Libraries

- Use memory-safe languages (Go, Rust) where possible
- Use **hard-to-misuse** libraries: `Libsodium crypto_secretbox_easy()` > OpenSSL > custom code
- **Monitor dependencies** for new vulnerabilities (GitHub Dependabot)
- **Never implement your own crypto** — almost always repeats others' mistakes

### Safer C Alternatives

```c
// UNSAFE — no bounds checking:
char *gets(char *buffer);

// SAFE — requires size parameter:
char *gets_s(char *buffer, size_t sizeInCharacters);
```

## Analysis Tools

| | **Static (SAST)** | **Dynamic** |
|--|-------------------|-------------|
| When | Without executing | On running program |
| False positives | Higher | Lower |
| False negatives | Higher | Lower |
| Examples | Cppcheck, semgrep, CodeQL | Valgrind, AFL++, Clang sanitizers |

### Fuzzing

**Fuzzing** = generating random/semi-random inputs to find crashes or unexpected behavior.

**Process:** investigate I/O → prepare data model → generate fuzzed inputs → send to app → monitor crashes → analyze.

**Key fuzzing tools:** AFL++, Radamsa, OWASP ZAP, Peach (grammar-based).  
**Coverage is crucial** — only visited code paths are tested.

### Taint Analysis

Tracks propagation of untrusted (tainted) values:
- **Taint sources:** attacker-controlled inputs (files, stdin, network)
- **Taint sinks:** sensitive operations (system(), SQL query, html_output)
- Alert fired when tainted value reaches a sink without sanitization

### Sanitizers (Clang)

Code instrumentation at compile-time detecting issues at runtime.  
**AddressSanitizer, ThreadSanitizer, UndefinedBehaviorSanitizer** — use with fuzzers for maximum coverage.

## Static vs. Dynamic False Positives

| | **False Positives** | **False Negatives** |
|--|---------------------|---------------------|
| Problem | Developers ignore tool reports | Real bugs are missed |
| Who tolerates | Exploit researchers (few real bugs) | Nobody |
| Who can't tolerate | Ordinary developers | Security-critical contexts |

## Security Coding — Cryptography Issues

**Common issues found:**
- Hardcoded passwords or keys in source code
- Weak algorithms: DES, MD5, ECB mode
- Insufficient randomness: `rand()`, low entropy after boot
- Custom "encryption" (XOR, security by obscurity)
- Key distribution/revocation not handled
- Certificate problems: self-signed, expired, missing intermediate CA

**Rules:** never design/implement your own crypto; use well-established library primitives; use standards (OAuth, OIDC, JWT).

## Continuous Integration (CI)

- Integrate **static and dynamic analysis** into CI pipeline
- Static analysis → even on unfinished code (pre-commit)
- Dynamic analysis → needs tests for coverage; run overnight
- **GitHub Dependabot:** automatically alerts/fixes vulnerable dependencies

## AI-Assisted Coding

- Treat **AI-generated code as an external Pull Request** — review critically
- AI tools helpful but **non-deterministic** and have **limited context** → risk of false confidence
- Treat agentic tooling as potentially malicious insider — what prevents harm?

## Typical Real-World Problems

| Problem | Details |
|---------|---------|
| **Developer education** | Training is expensive; most bugs are preventable |
| **Legacy code** | Too many issues; fixing can break things |
| **"Functionality first"** | Security added too late → architectural issues unfixable |
| **3rd-party dependencies** | No control; possibly unmaintained |
| **Security economics** | Those who pay for fix ≠ those who suffer |
| **Customers don't update** | Fear of breaking changes; small releases help |

## Key Points

- **Bug ≠ Vulnerability ≠ PoC ≠ Exploit** — understand the chain
- **5 layers:** source code, testing, compiler, execution environment, defence in depth
- **DEP + ASLR + CFI + stack canary** = core platform protections
- **Static:** fast, early, more FPs; **Dynamic:** lower FP, needs coverage
- **Fuzzing** is easy to start, finds low-hanging-fruit bugs
- **Taint analysis** tracks attacker-controlled values through code flow
- **Never implement your own crypto**
- **Security economics:** post-release fixing costs far more than during development

## Related

- [[../concepts/AES-DES|AES & DES]]
- [[../concepts/Hash-Functions|Hash Functions]]
- [[L10-NetworkAttacks-IDS|L10 – Network Attacks & IDS]]
- [[L11-UsableSecurity|L11 – Usable Security]]
