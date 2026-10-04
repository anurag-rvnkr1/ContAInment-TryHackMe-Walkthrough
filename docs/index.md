---
layout: default
title: "ContAInment — TryHackMe"
permalink: /
---

# 🛡️ ContAInment

## TryHackMe Incident Response Walkthrough

A premium portfolio write-up covering endpoint triage, PCAP investigation, data reconstruction, encrypted archive recovery, prompt-injection analysis, and AI-assisted incident response.

| Property | Value |
|---|---|
| Platform | TryHackMe |
| Room | ContAInment |
| Discipline | DFIR / Incident Response |
| Core Evidence | PCAP + workstation artifacts |
| Key Technique | PCAP reassembly + evidence-driven analysis |
| Final Result | Threat contained / challenge completed |

## Investigation Chain

```text
SSH Access
   ↓
Host Triage
   ↓
PCAP Discovery
   ↓
Evidence Prioritization
   ↓
PCAP Reassembly
   ↓
Prompt-Injection Analysis
   ↓
Archive Recovery
   ↓
Artifact Analysis
   ↓
Liberty Prime Validation
```

## 01 — Challenge Context

![Room overview](assets/01-room-overview.png)

The scenario places the analyst inside West Tech's compromised research environment. The objective is to reconstruct the intrusion and recover the relevant evidence.

## 02 — Establishing Access

![SSH access](assets/02-ssh-access.png)

SSH access provides an initial foothold for defensive investigation and evidence collection.

## 03 — Endpoint Triage

![Workstation reconnaissance](assets/03-workstation-recon.png)

Initial filesystem enumeration reveals normal user directories alongside suspicious investigation artifacts and an encrypted project archive.

## 04 — Network Evidence Discovery

![PCAP discovery](assets/04-pcap-discovery.png)

The `Documents` tree contains numerous dated PCAP session files. File size and naming patterns provide a first-pass prioritization method.

## 05 — Reconstructing a Candidate Session

![PCAP reassembly](assets/05-ir-assistant-reassembly.png)

The challenge's internal IR assistant can reassemble a selected PCAP into a readable artifact.

## 06 — Reassembled Evidence

![Reassembled artifact](assets/06-reassembled-artifact.png)

The recovered text contains operational data and a prompt-injection trail. Embedded instructions are treated as attacker-controlled content, not trusted assistant instructions.

## 07 — Archive Recovery

![Encrypted project archive](assets/07-encrypted-projects.png)

The investigation then moves to the recovered encrypted project archive and its extracted contents.

## 08 — Project Artifact Analysis

![Project contents](assets/08-project-contents.png)

The extracted corpus contains multiple documents, logs, exports, specifications, and the challenge's final evidence container.

## 09 — Liberty Prime

![Liberty Prime](assets/09-liberty-prime-ui.png)

A challenge-specific analysis tool is used to inspect the known flag artifact. In a real incident, this result should be independently verified against the source evidence.

## 10 — Result

![Final evidence](assets/10-flag-redacted-evidence.png)

The actual challenge answer is intentionally redacted from this public portfolio:

```text
THM{REDACTED_FOR_PUBLICATION}
```

## Key Lessons

- Evidence provenance matters.
- File size is a prioritization signal, not proof.
- PCAP reconstruction can turn low-level traffic into high-level forensic artifacts.
- Prompt injection can occur inside recovered incident data.
- AI-assisted IR must maintain strict trust boundaries.
- Critical findings should be validated against source artifacts.

## Full Walkthrough

[Read the complete technical documentation](../Documentation/Documentation.md)

## Repository

[GitHub](https://github.com/anurag-rvnkr1/ContAInment-TryHackMe-Walkthrough)

---

**Author:** Anurag Revankar
