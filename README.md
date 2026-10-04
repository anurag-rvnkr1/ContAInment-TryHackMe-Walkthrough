# ContAInment — TryHackMe CTF Walkthrough

> **Room:** ContAInment  
> **Focus:** Digital Forensics / Incident Response / PCAP Analysis / AI-Assisted Investigation  
> **Theme:** Ransomware, data exfiltration, evidence reconstruction, prompt injection

A premium portfolio-oriented walkthrough documenting the investigation of a compromised West Tech research workstation.

## Attack / Investigation Chain

`SSH Access → Host Triage → PCAP Discovery → Evidence Prioritization → PCAP Reassembly → Prompt-Injection Analysis → Archive Recovery → Artifact Analysis → Liberty Prime Validation`

## Core Skills Demonstrated

- Linux endpoint triage
- PCAP discovery and evidence prioritization
- Network-stream reconstruction
- Archive recovery and file-system analysis
- Prompt-injection identification
- AI-assisted incident response
- Evidence provenance and validation
- Defensive recommendations

## Public Flag Policy

The final TryHackMe flag is intentionally redacted from the public portfolio documentation:

`THM{REDACTED_FOR_PUBLICATION}`

## Documentation

- [Full Technical Walkthrough](Documentation/Documentation.md)
- [Word Document](Documentation/Documentation.docx)
- [GitHub Pages Version](docs/index.md)
- [Technical Notes](Resources/notes.md)

## Repository Structure

```text
.
├── Documentation/
│   ├── Documentation.md
│   ├── Documentation.docx
│   └── README.md
├── Resources/
│   └── notes.md
├── Screenshots/
│   ├── 01-room-overview.png
│   ├── 02-ssh-access.png
│   ├── 03-workstation-recon.png
│   ├── 04-pcap-discovery.png
│   ├── 05-ir-assistant-reassembly.png
│   ├── 06-reassembled-artifact.png
│   ├── 07-encrypted-projects.png
│   ├── 08-project-contents.png
│   ├── 09-liberty-prime-ui.png
│   └── 10-flag-redacted-evidence.png
├── docs/
│   ├── index.md
│   └── assets/
│       └── ...
├── _config.yml
└── LICENSE
```

## Responsible Use

This documentation covers an intentionally vulnerable TryHackMe challenge environment. Do not apply the techniques or credentials shown in the lab against systems without explicit authorization.

## Author

**Anurag Revankar**  
[GitHub](https://github.com/anurag-rvnkr1)
