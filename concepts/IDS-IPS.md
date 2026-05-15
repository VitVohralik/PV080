---
tags: [pv080, concept, ids, ips, network-security]
---

# IDS / IPS

> Related lectures: [[../lectures/L10-NetworkAttacks-IDS|L10]]

## Definitions

| Term | Definition |
|------|-----------|
| **Intrusion** | Event on a host or network that violates security policy |
| **Intrusion Detection** | Passive monitoring and analysing events to identify and report intrusions |
| **IDS** (Intrusion Detection System) | Automates event analysis to **report events** requiring human attention |
| **IPS** (Intrusion Prevention System) | Automates **real-time active responses** to stop attacks |

## IDS Architecture

**Host-based IDS:**
- Events from logs (application, kernel), audit records
- Data must be collected centrally for cross-host views

**Network-based IDS:**
- Events from packets at a strategic vantage point
- Provides network-wide situational awareness

## Detection Methods

| Method | Alarm when... | Key properties |
|--------|--------------|---------------|
| **Signature-based** | Events match known-bad patterns | Fast, accurate; detects **only known attacks** |
| **Specification-based** | Events deviate from expert-defined specs | Can detect new attacks; specs are protocol/program-specific |
| **Anomaly-based** | Events deviate from learned normal profile | Can detect new attacks; higher FP rate; needs training period |

## False Positives and Negatives

|  | **Intrusion exists** | **No intrusion** |
|--|---------------------|-----------------|
| **Alarm raised** | **True Positive (TP)** ✓ | **False Positive (FP)** — false alarm |
| **No alarm raised** | **False Negative (FN)** ✗ — missed! | **True Negative (TN)** ✓ |

- **FN (missed intrusion)** = most dangerous outcome
- **FP (false alarm)** = wastes analyst time, causes alert fatigue
- Threshold value shifts balance between FP and FN

## Network Monitoring

| | **Active Monitoring** | **Passive Monitoring** |
|--|---------------------|----------------------|
| How | Sends probes into network | Observes traffic at observation point |
| Pros | Detailed info | Transparent; no added traffic |
| Cons | Increases link utilization; target aware | Encrypted traffic issues |
| Tools | nmap, Shodan | Wireshark, tcpdump, Zeek |

### Packet Capture Methods

- **Hub** — broadcasts all frames → easy to capture
- **Switch** — sends only to destination MAC → isolates traffic
- **Promiscuous mode** — NIC captures all frames (not just addressed to it)
- **SPAN** (Switch Port Mirroring) — switch copies traffic to analyzer port
- **TAP** (Test Access Point) — physical in-line capture

## Network-Based Attacks

### DDoS (Distributed Denial of Service)

| Type | Example |
|------|---------|
| **Volume-based** | UDP floods |
| **Protocol attacks** | SYN floods |
| **Application-layer** | HTTP floods |

**SYN Flooding with Spoofed IPs:**
1. Attacker sends SYN with forged source IPs
2. Victim replies SYN-ACK to spoofed addresses
3. Victim's connection table fills up → legitimate connections rejected

**Mitigation:**
- **Ingress filtering** — blocks packets with spoofed source IPs entering a network
- **Egress filtering** — prevents spoofed packets leaving a network
- Firewalls, traffic limiting, DDoS protection services

### ARP Spoofing

**ARP** maps IPv4 addresses to MAC addresses on local networks.

**Attack:**
1. MAC flooding → forces switch to broadcast all frames
2. Fake ARP replies → poisons ARP caches of victim/gateway
3. Victim's ARP cache: `<IP_gateway, MAC_attacker>` → traffic goes to attacker

**Goal:** Associate attacker's MAC with IP of gateway → MITM.  
**Mitigation:** Static ARP entries, Dynamic ARP Inspection (switch port security).

## Vulnerability Assessment

| Tool type | Purpose |
|-----------|---------|
| **Reconnaissance tools** | Network mapping (nmap, p0f) |
| **Vulnerability scanners** | Automated CVE-based scanning |
| **Penetration testing tools** | Exploitation (Metasploit, Kali Linux) |

**CVE** = Common Vulnerabilities and Exposures — public list of known vulnerabilities.  
~260,000 total; ~32% have weaponized exploits; ~3% are zero days (accelerating growth post-2015).

**MITRE ATT&CK** = knowledge base of adversary tactics and techniques based on real-world observations.

## Related

- [[Risk-Assessment|Risk Assessment]]
- [[../lectures/L10-NetworkAttacks-IDS|L10 – Network Attacks & IDS]]
- [[../lectures/L09-SecurityPolicy-Risk|L09 – Security Policy & Risk]]
