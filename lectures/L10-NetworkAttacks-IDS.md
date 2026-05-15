---
tags: [pv080, lecture, network-security, ids, attacks]
lecture: 10
---

# L10 – Network Attacks, IDS & Vulnerability Assessment

> Full notes: [[../fulltext|fulltext]]

## Network Monitoring

| | **Active Monitoring** | **Passive Monitoring** |
|--|---------------------|----------------------|
| How | Sends probes | Observes traffic at observation point |
| Pros | Detailed info; various methods | Transparent; no added traffic |
| Cons | Inserts traffic; target may detect | Limited info; encrypted traffic issues |
| Tools | **nmap**, Shodan, Internet Census | **Wireshark** (tshark), tcpdump, **Zeek** |

## Packet Capture

- **Hub** — broadcasts all frames → easy to capture all traffic
- **Switch** — sends only to destination MAC → isolates traffic; need special setup to capture
- **Promiscuous mode** — NIC captures all frames (not just addressed to it)
- **SPAN** (Switch Port Mirroring) — switch copies traffic to analyzer port
- **In-line (TAP)** — analyzer placed physically in-line

## Nmap — Port Scanning

- **Host discovery** — which targets are online
- **Port scanning (TCP SYN)** — classifies ports: open / closed / filtered
- **OS and application fingerprinting** — identifies remote OS and services

## Intrusion Detection Systems

See [[../concepts/IDS-IPS|IDS/IPS]] for full details.

| Term | Definition |
|------|-----------|
| **IDS** | Automates event analysis to **report** events needing human attention |
| **IPS** | Automates **real-time active responses** to stop attacks |

### Architecture

- **Host-based IDS** — events from logs, audit records; centralized collection
- **Network-based IDS** — events from packets; network-wide visibility

### Detection Methods

| Method | Detects | Pros | Cons |
|--------|---------|------|------|
| **Signature-based** | Known attacks only | Fast, few FPs | Misses new attacks |
| **Specification-based** | New attacks (expert-defined specs) | Can detect new attacks | Specs are protocol-specific |
| **Anomaly-based** | New attacks (learned profile) | Can detect new attacks | Higher FP rate; needs training |

**IDS** can be host-based or network-based. No one-size-fits-all approach.

## False Positives and Negatives

|  | **Intrusion exists** | **No intrusion** |
|--|---------------------|-----------------|
| **Alarm raised** | **True Positive (TP)** ✓ | **False Positive (FP)** — false alarm |
| **No alarm raised** | **False Negative (FN)** — missed! ✗ | **True Negative (TN)** ✓ |

**FN (missed intrusion)** = most dangerous.  
**FP (false alarm)** = wastes analyst time, causes alert fatigue.

## Vulnerability Assessment

**Process:** identifying, quantifying, and prioritizing vulnerabilities in a system.

| Tool type | Purpose | Examples |
|-----------|---------|---------|
| **Reconnaissance** | Network mapping | nmap, p0f |
| **Vulnerability scanners** | Automated CVE-based scanning | Nessus, OpenVAS |
| **Penetration testing** | Exploitation | Metasploit, Kali Linux |

**CVE** — Common Vulnerabilities and Exposures: ~260,000 total; ~32% have weaponized exploits; ~3% are zero days.

**MITRE ATT&CK** = knowledge base of adversary tactics and techniques (attack.mitre.org).

## Network-Based Attacks

### DDoS (Distributed Denial of Service)

Overwhelms server/network with traffic from many sources.

| Type | Example |
|------|---------|
| **Volume-based** | UDP floods |
| **Protocol attacks** | SYN floods |
| **Application-layer** | HTTP floods |

**Reflection DDoS:** attacker spoofs victim's IP → reflectors (open DNS servers) flood victim.

**SYN Flooding with Spoofed IPs:**
1. SYN with forged source IPs → victim replies SYN-ACK to spoofed addresses
2. Victim's connection table fills up → legitimate connections rejected

**Mitigation:**
- **Ingress filtering** — block packets with spoofed source IPs entering network
- **Egress filtering** — prevent spoofed packets leaving network
- Firewalls, traffic limiting, DDoS protection services

### ARP Spoofing

**ARP** maps IPv4 addresses to MAC addresses on local networks.

**Attack:**
1. MAC flooding → forces switch to broadcast
2. Fake ARP replies → poisons ARP caches
3. Victim's ARP cache: `<IP_gateway, MAC_attacker>` → traffic goes to attacker (MITM)

**Enables:** data interception, session hijacking, MITM.  
**Mitigation:** static ARP entries, Dynamic ARP Inspection.

## Firewall

- Typical operations: packet filtering, stateful inspection, NAT, logging
- Filters based on source/destination IP, port, protocol
- Cannot inspect encrypted traffic; does not protect against insider threats

## Key Points

- **Active monitoring** sends probes (nmap); **passive** observes traffic (Wireshark, Zeek)
- **IDS reports; IPS blocks** — both collect events from sensors
- **Signature-based:** known attacks only; **anomaly-based:** new attacks but more FPs
- **FN (missed intrusion)** is the most dangerous detection failure
- **CVE** = public vulnerability database; 32% have weaponized exploits
- **DDoS:** direct flooding vs. reflection; SYN flood exhausts connection table
- **Ingress/egress filtering** mitigates IP spoofing-based DDoS
- **ARP spoofing** → MITM; mitigation: static ARP entries

## Related

- [[../concepts/IDS-IPS|IDS/IPS]]
- [[../concepts/Risk-Assessment|Risk Assessment]]
- [[L09-SecurityPolicy-Risk|L09 – Security Policy & Risk]]
- [[L12-SecureProgramming|L12 – Secure Programming]]
