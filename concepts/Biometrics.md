---
tags: [pv080, concept, biometrics, authentication, iam]
---

# Biometrics

> Related lectures: [[../lectures/L05-IAM-Biometrics|L05]]

## Two Deployment Modes

1. **Verification (Authentication)** — is this person who they claim to be? → **1:1 comparison**
2. **Identification** — who is this person? → **1:N search** through entire database

## Key Properties

- **Biometrics are NOT secrets** — must be combined with other factors
- **Not suitable for remote authentication alone** — remote biometric auth is two-staged: user-to-device (biometric), device-to-service (cryptographic)
- **Biometrics of the same person are never 100% identical** → threshold matching required
- Fallback mechanisms always necessary

## Error Rates

| Metric | Definition |
|--------|-----------|
| **FAR** (False Acceptance Rate) | Impostor accepted — threshold too lenient |
| **FRR** (False Rejection Rate) | Legitimate user rejected — threshold too strict |
| **FTE** (Failure to Enrol) | Unsuccessful attempt to register a template |
| **FTC** (Failure to Capture) | Bad quality sample during authentication |
| **EER** (Equal Error Rate) | Point where FAR = FRR — used for system comparison |

### FAR vs FRR Trade-off

- Lowering threshold (lenient) → **more FAR** (more impostors accepted), **less FRR**
- Raising threshold (strict) → **less FAR**, **more FRR**

**If unauthorized users are "greater evil" than dissatisfied users → lower FAR → raise threshold → more FRR.**

## Evaluation Criteria for Biometrics

| Criterion | Question |
|-----------|---------|
| **Universality** | Do all users have this trait? |
| **Distinguishability** | Are there enough differences between users? |
| **Ease-of-sampling** | How easy to acquire a sample? |
| **Cost** | Processing time, storage, hardware/software |
| **User acceptance** | Will users accept it? |
| **Attack-resistance** | How hard to circumvent? |

## Fingerprint Biometrics

### Feature Levels

| Level | Features |
|-------|---------|
| **Level 1** | Global pattern: arch, tented arch, left/right loop, double loop, whorl |
| **Level 2** | Minutiae: line-unit, fragment, ending, bifurcation, eye, hook |
| **Level 3** | Fine details: pores, line shape, incipient ridges, creases, scars |

### Sensor Types

| Type | Mechanism | Vulnerability |
|------|----------|--------------|
| **Optical** | 2D image; oldest | Easily fooled by fake fingers |
| **Capacitive** | Capacitor arrays; ridges change charge | Material-dependent |
| **Ultrasonic** | Signal return time; in-display | More resistant to fakes |
| **Thermal** | Temperature differences | — |

### Liveness Detection

Tests finger's reaction to sensor stimuli: temperature, skin resistance, pulse/blood flow.  
Defends against latent fingerprints, replay, fake fingers (gelatin, silicone).

## Face Recognition

### Approaches

| Type | Examples |
|------|---------|
| **Statistical methods** | Eigenface, PCA, LDA |
| **Neural networks** | Microsoft Face API, Facebook DeepFace, Google FaceNet |

**OpenFace pipeline:** Input image → Detect → Align → Crop → DNN → 128D representation → Classification/Similarity

### Attacks

- **CV Dazzle:** make-up/hair patterns defeating face detection
- **Adversarial glasses (CMU 2016):** specially printed glasses fool DNNs with >90% success

## Commercial vs. Forensic Systems

| | Commercial | Forensic |
|--|-----------|---------|
| Precision | Lower | High |
| Enrollment | Can repeat | Once only |
| Data stored | Extracted features/template only | Full raw biometric data |
| Speed | Fast, automatic | Slower, expert review |
| Cost | Cheap | Costly |

## Most Accurate Biometric Techniques

- **Iris recognition** and **fingerprint** are among the most accurate.
- Most convenient for users: **face recognition** and **voice recognition**.
- Cheapest: **fingerprint** and **face recognition**.

## Related

- [[Authentication|Authentication]]
- [[../lectures/L05-IAM-Biometrics|L05 – IAM & Biometrics]]
