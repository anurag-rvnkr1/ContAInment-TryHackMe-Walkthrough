---
title: "ContAInment — Incident Response and Digital Forensics Walkthrough"
description: "A technical incident-response investigation covering endpoint triage, PCAP analysis, evidence reconstruction, prompt injection, encrypted archive recovery, and AI-assisted forensic workflows in the TryHackMe ContAInment room."
---

# ContAInment — Incident Response and Digital Forensics Walkthrough

**Platform:** TryHackMe  
**Discipline:** Digital Forensics and Incident Response (DFIR)  
**Investigation Focus:** Endpoint triage, network evidence analysis, artifact reconstruction, archive recovery, and AI-assisted incident response

## Navigation

<div class="ctf-toc">
  <div class="ctf-toc-title">Documentation Map</div>
  <ul>
    <li><a href="#executive-summary">Executive Summary</a></li>
    <li><a href="#quick-overview">Quick Overview</a></li>
    <li><a href="#mission-and-investigation-objectives">Mission and Investigation Objectives</a></li>
    <li><a href="#scope-and-responsible-use">Scope and Responsible Use</a></li>
    <li><a href="#investigation-methodology">Investigation Methodology</a></li>
    <li><a href="#investigation-timeline">Investigation Timeline</a></li>
    <li><a href="#phase-1-challenge-context">Phase 1 — Challenge Context</a></li>
    <li><a href="#phase-2-establishing-initial-access">Phase 2 — Establishing Initial Access</a></li>
    <li><a href="#phase-3-endpoint-triage">Phase 3 — Endpoint Triage</a></li>
    <li><a href="#phase-4-network-evidence-discovery">Phase 4 — Network Evidence Discovery</a></li>
    <li><a href="#phase-5-pcap-investigation-and-reconstruction">Phase 5 — PCAP Investigation and Reconstruction</a></li>
    <li><a href="#phase-6-prompt-injection-analysis">Phase 6 — Prompt-Injection Analysis</a></li>
    <li><a href="#phase-7-encrypted-archive-recovery">Phase 7 — Encrypted Archive Recovery</a></li>
    <li><a href="#phase-8-project-artifact-analysis">Phase 8 — Project Artifact Analysis</a></li>
    <li><a href="#phase-9-liberty-prime-validation">Phase 9 — Liberty Prime Validation</a></li>
    <li><a href="#phase-10-final-evidence-and-result">Phase 10 — Final Evidence and Result</a></li>
    <li><a href="#investigation-chain">Investigation Chain</a></li>
    <li><a href="#evidence-handling-and-provenance">Evidence Handling and Provenance</a></li>
    <li><a href="#key-findings-and-security-implications">Key Findings and Security Implications</a></li>
    <li><a href="#defensive-recommendations">Defensive Recommendations</a></li>
    <li><a href="#lessons-learned">Lessons Learned</a></li>
    <li><a href="#limitations-and-validation-notes">Limitations and Validation Notes</a></li>
    <li><a href="#challenge-answer">Challenge Answer</a></li>
    <li><a href="#conclusion">Conclusion</a></li>
    <li><a href="#references-and-resources">References and Resources</a></li>
    <li><a href="#repository">Repository</a></li>
    <li><a href="#author">Author</a></li>
  </ul>
</div>

---

## Executive Summary

ContAInment is a TryHackMe digital forensics and incident response (DFIR) challenge centered on investigating a compromised research environment associated with West Tech.

The investigation follows an evidence-driven workflow that begins with access to the target workstation and progresses through endpoint triage, network capture discovery, packet-capture reconstruction, prompt-injection analysis, encrypted archive recovery, and examination of recovered project artifacts.

An internal incident-response assistant provides a mechanism for reconstructing selected PCAP evidence into a more readable artifact. The resulting content includes operational information and a prompt-injection trail, introducing an important security consideration: incident data may contain adversarial instructions that must remain untrusted throughout an AI-assisted investigation.

The final phase uses a challenge-specific tool named Liberty Prime to inspect the known evidence artifact associated with the challenge result.

This walkthrough documents the supplied investigation sequence and its forensic significance. Exact commands, packet identifiers, archive passwords, cryptographic hashes, and underlying artifact contents are not asserted where they were not provided in the source material.

## Quick Overview

| Property | Details |
|---|---|
| Platform | TryHackMe |
| Room | ContAInment |
| Discipline | Digital Forensics and Incident Response |
| Environment | West Tech research environment |
| Initial access | SSH access |
| Primary evidence | PCAP files and workstation artifacts |
| Network analysis | PCAP prioritization and reconstruction |
| Additional investigation | Encrypted archive and project artifact analysis |
| AI security focus | Prompt injection within recovered incident data |
| Challenge-specific utility | Liberty Prime |
| Reported outcome | Challenge completed; threat-containment scenario investigated |
| Public challenge answer | Intentionally redacted |

### Skills Demonstrated

- Endpoint reconnaissance and filesystem triage
- Network evidence discovery and prioritization
- Packet-capture investigation
- Reconstruction of network-derived artifacts
- Analysis of potentially adversarial text
- Encrypted archive investigation
- Correlation of recovered project files
- Trust-boundary analysis for AI-assisted investigations
- Evidence provenance and result validation
- Technical reporting and defensive recommendations

## Mission and Investigation Objectives

The primary objective is to reconstruct the relevant incident evidence from the compromised research environment and determine how the discovered artifacts contribute to the challenge's final result.

The investigation is organized around the following objectives:

1. Establish access to the challenge environment.
2. Inspect the workstation for potentially relevant evidence.
3. Locate and prioritize available PCAP session files.
4. Reconstruct a candidate network session using the challenge's internal IR assistant.
5. Analyze the reconstructed content without treating embedded instructions as trusted commands.
6. Investigate the encrypted project archive and its extracted contents.
7. Examine the available project artifacts and locate the designated evidence container.
8. Use Liberty Prime to inspect the known challenge artifact.
9. Preserve the public report's evidence boundaries by redacting the challenge answer.

The workflow emphasizes a central DFIR principle: **evidence should drive the investigation, and conclusions should remain proportional to what the evidence actually establishes.**

## Scope and Responsible Use

This report concerns the authorized TryHackMe ContAInment lab.

The investigation scope includes:

- The challenge-provided workstation and filesystem.
- PCAP files available inside the lab.
- Artifacts reconstructed through the challenge's internal IR assistant.
- The encrypted project archive and its extracted contents.
- The challenge-specific Liberty Prime interface.
- Defensive lessons derived from the investigation workflow.

The walkthrough does not claim access to West Tech's real-world infrastructure or establish that the simulated activity represents an actual incident.

All investigation steps should remain within the authorized challenge environment. Evidence from real systems should be handled according to applicable authorization, privacy, retention, and incident-response requirements.

## Investigation Methodology

The investigation follows a staged forensic workflow.

| Stage | Investigation activity | Purpose |
|---|---|---|
| 1 | Initial access | Enter the authorized investigation environment |
| 2 | Endpoint triage | Identify relevant directories and candidate evidence |
| 3 | Network evidence discovery | Locate and prioritize PCAP files |
| 4 | PCAP reconstruction | Convert selected network evidence into readable content |
| 5 | Content analysis | Identify operational data and suspicious instructions |
| 6 | Archive investigation | Examine the encrypted project archive |
| 7 | Artifact correlation | Review extracted files and locate the final evidence container |
| 8 | Result validation | Inspect the challenge artifact using Liberty Prime |

The stages represent the sequence documented in the challenge overview. They should not be interpreted as proof that every possible forensic source was examined.

### Evidence-Driven Prioritization

The presence of numerous dated PCAP files creates a triage problem: investigators must decide which artifacts to examine first.

File names, timestamps, sizes, directory locations, and relationships to other artifacts can help prioritize candidates. These characteristics are investigative leads, not proof that a particular file contains malicious activity or the required evidence.

### Analysis and Validation

The investigation distinguishes three levels of confidence:

- **Observed:** A fact directly visible in a supplied screenshot, file, log, or tool output.
- **Inferred:** A conclusion supported by one or more observations but not independently confirmed.
- **Validated:** A conclusion corroborated against the underlying source evidence or an independent verification method.

This distinction is particularly important when using automated reconstruction tools or AI-assisted analysis.

## Investigation Timeline

The following sequence reflects the supplied challenge walkthrough. Exact timestamps for the individual investigation stages were not provided.

1. Establish SSH access to the lab.
2. Perform initial workstation reconnaissance.
3. Locate the collection of dated PCAP session files.
4. Select a candidate session for further analysis.
5. Reconstruct the selected session using the internal IR assistant.
6. Examine the reconstructed artifact for operational data and prompt-injection content.
7. Investigate the encrypted project archive.
8. Examine the extracted project corpus.
9. Use Liberty Prime to inspect the designated evidence artifact.
10. Record the outcome while keeping the public challenge answer redacted.

---

## Phase 1 — Challenge Context

![Room overview](../docs/assets/01-room-overview.png)

The scenario places the analyst inside West Tech's research environment following a simulated security incident.

The objective is to identify and reconstruct relevant evidence rather than rely on a single indicator or an unexplained final result.

The available investigation path includes workstation artifacts, network capture files, recovered content, and a project archive. These sources provide multiple points at which evidence can be prioritized, interpreted, and validated.

### Investigative Significance

A compromised research environment may contain evidence across several layers:

- Filesystem artifacts that indicate where relevant data is stored.
- Network captures that preserve recorded communications.
- Reconstructed application or session content.
- Archives that contain supporting project material.
- Specialized tools that help interpret challenge-specific artifacts.

A sound investigation considers the relationships among these sources instead of treating any one source as a complete account of the incident.

**Evidence note:** The scenario description establishes the lab's investigative context. Specific initial-access vectors, malware families, attacker identities, and persistence mechanisms are not established by the supplied material.

## Phase 2 — Establishing Initial Access

![SSH access](../docs/assets/02-ssh-access.png)

SSH access provides the initial foothold used to inspect the challenge workstation.

After entering an authorized environment, an investigator generally establishes the working context before collecting or interpreting evidence. Relevant checks include the current user, working directory, accessible filesystem locations, and the scope of the assigned system.

### Investigation Objectives

- Confirm that the session is connected to the intended lab.
- Identify the accessible filesystem and relevant user directories.
- Locate candidate investigation artifacts.
- Avoid modifying evidence unnecessarily during initial triage.

### Forensic Considerations

SSH access establishes an interactive session, but it does not by itself demonstrate how an attacker originally entered the simulated environment.

In an actual incident, investigators should distinguish between the access mechanism provided for the investigation and the intrusion vector under examination.

### Evidence Interpretation

The supplied screenshot documents the SSH-access stage. It does not independently establish the authentication method, original compromise mechanism, or complete command history.

## Phase 3 — Endpoint Triage

![Workstation reconnaissance](../docs/assets/03-workstation-recon.png)

Initial filesystem enumeration reveals ordinary user directories alongside investigation artifacts and an encrypted project archive.

Endpoint triage is the process of rapidly identifying data sources that may help explain an incident. The objective is to establish where potentially relevant evidence resides and which sources should receive deeper analysis.

### Triage Priorities

A structured workstation review can examine:

- User directories and documents.
- Recently modified or otherwise relevant files.
- Network capture collections.
- Archives and extracted project directories.
- Logs, exports, specifications, and other supporting artifacts.
- File relationships that may reveal a useful investigation sequence.

### Prioritizing Artifacts

The existence of a file does not establish its relevance. A useful triage process considers multiple attributes, including:

| Attribute | Investigative value | Limitation |
|---|---|---|
| File name | May suggest the file's purpose | Names can be misleading or deliberately changed |
| Directory path | Provides storage context | Location alone does not prove relevance |
| File size | Helps prioritize unusually large or small files | Size is not evidence of maliciousness |
| Timestamps | May support event correlation | Metadata can be altered or affected by copying |
| File format | Helps select appropriate analysis tools | Extensions may not match actual content |
| Related artifacts | May reveal a common investigation thread | Relationships must be corroborated |

### Expected Outcome

This phase narrows the available material to candidate network captures and project artifacts for closer examination.

The supplied evidence does not provide a complete filesystem inventory or a definitive list of all files present on the workstation.

## Phase 4 — Network Evidence Discovery

![PCAP discovery](../docs/assets/04-pcap-discovery.png)

The workstation's `Documents` tree contains multiple dated PCAP session files.

A PCAP file records captured network packets and can preserve information about communications, protocol behavior, transferred data, and application activity. Its investigative value depends on the traffic captured, the capture point, and the completeness of the recording.

### Why PCAP Files Matter

Network evidence can help investigators answer questions such as:

- Which protocols were involved in a recorded communication?
- What endpoints appear in the captured traffic?
- Was application content transmitted in a recoverable form?
- Do captured sessions correspond to other files or event records?
- Can a particular session explain how a suspicious artifact was created or transferred?

These are general investigative questions, not findings that have been independently established for every capture in this challenge.

### Candidate Prioritization

Because the capture directory contains multiple dated files, an efficient workflow prioritizes candidates rather than assuming that the first file is the most relevant.

Useful criteria include:

1. File integrity and readability.
2. Capture time and the period covered.
3. File size and packet volume.
4. Protocols and endpoints represented.
5. Relationships to the workstation's other artifacts.
6. Whether the capture can be associated with the reconstructed content.

File size and naming patterns are useful for triage, but neither is sufficient to establish the contents or importance of a PCAP.

### Recommended Validation

For a production investigation, record the selected capture's original path, file size, cryptographic hash, relevant timestamps, and the reason for selecting it. Preserve the original capture and perform analysis on a working copy where feasible.

The supplied walkthrough establishes that multiple PCAP files were discovered, but does not specify the exact filename, hash, packet count, or timestamp range of the candidate ultimately selected.

## Phase 5 — PCAP Investigation and Reconstruction

![PCAP reassembly](../docs/assets/05-ir-assistant-reassembly.png)

The challenge's internal IR assistant can reassemble a selected PCAP into a readable artifact.

This step bridges low-level network evidence and higher-level incident interpretation. Instead of reviewing every packet manually, reconstruction can expose content in a form that is easier to examine and correlate with other artifacts.

### Reconstruction Workflow

The documented process can be summarized as follows:

1. Identify a candidate PCAP during triage.
2. Select the candidate for reconstruction using the challenge's IR assistant.
3. Review the generated artifact.
4. Identify relevant operational information and suspicious content.
5. Preserve the connection between the reconstructed output and its source capture.
6. Continue analysis using the reconstructed content as a lead, not as an unquestionable conclusion.

### Why Reconstruction Requires Caution

Network reassembly is an interpretation process. Its output may depend on the tool's parsing, protocol support, session selection, and handling of incomplete or ambiguous traffic.

A readable artifact can make analysis easier, but it should not replace the original packet-level evidence.

For independent verification, an analyst should establish:

- Which source PCAP was processed.
- Which session or traffic flow was selected.
- Whether the reconstruction completed successfully.
- Whether the output corresponds to the original captured data.
- Whether the reconstructed content can be corroborated using another method.

### Evidence Provenance

The reconstructed artifact should remain linked to the original PCAP. In a formal incident-response report, record the tool used, relevant settings, processing time, source-file hash, output-file hash, and any limitations affecting the reconstruction.

The supplied material confirms the use of the internal IR assistant but does not identify its implementation, reconstruction algorithm, or detailed output metadata.

## Phase 6 — Prompt-Injection Analysis

![Reassembled artifact](../docs/assets/06-reassembled-artifact.png)

The reconstructed content contains operational data and a prompt-injection trail.

This introduces a significant security consideration for AI-assisted incident response: recovered content may include instructions intended to manipulate an AI system that processes or summarizes the evidence.

### The Trust-Boundary Problem

Incident artifacts should be treated as untrusted data, even when they contain text formatted as instructions, policies, system messages, or commands.

A malicious or misleading artifact might attempt to:

- Override the investigator's stated objective.
- Instruct an assistant to ignore previous directions.
- Encourage disclosure of sensitive information.
- Request unauthorized tool use or external actions.
- Misrepresent evidence or influence the final report.
- Direct the assistant to suppress contradictory findings.

These examples describe general prompt-injection risks. They should not be interpreted as the exact payload or wording found in the challenge unless verified against the recovered artifact.

### Safe Analysis Principles

The correct response to embedded instructions is to analyze them as evidence rather than follow them.

A defensible AI-assisted workflow should:

1. Preserve the original content.
2. Identify potentially adversarial instructions.
3. Keep recovered text separate from trusted analyst instructions.
4. Avoid granting untrusted content authority over tools or system behavior.
5. Validate factual claims against source evidence.
6. Record when an AI-generated interpretation influenced the investigation.
7. Require human review before consequential actions or final conclusions.

### Data Is Not Authority

The fundamental distinction is between the *content being investigated* and the *instructions governing the investigation*.

A packet capture, recovered document, log entry, or archive may contain text that looks authoritative. Its appearance does not make it trustworthy.

AI tools should not automatically execute instructions discovered inside evidence. Their role is to help interpret the material within an explicitly defined and controlled investigative scope.

### Investigative Outcome

The supplied walkthrough identifies prompt injection as part of the reconstructed evidence. The specific payload, execution behavior, and any resulting compromise of the assistant are not documented in the supplied source.

Accordingly, this report treats prompt injection as an observed investigation theme rather than claiming that a particular system was successfully compromised.

## Phase 7 — Encrypted Archive Recovery

![Encrypted project archive](../docs/assets/07-encrypted-projects.png)

After examining the reconstructed network evidence, the investigation proceeds to an encrypted project archive.

Encrypted archives can preserve collections of documents and supporting artifacts while preventing direct access to their contents without the appropriate recovery material.

### Investigation Objectives

- Identify the archive and establish its relationship to the investigation.
- Determine whether the archive is readable and structurally valid.
- Establish the appropriate authorized recovery method within the challenge.
- Extract the contents while preserving the original archive.
- Record the relationship between the archive and the extracted files.
- Check whether the extracted contents include evidence relevant to the investigation.

### Evidence Handling

Archive extraction introduces a new set of artifacts. The extracted directory should be treated as a derivative evidence set, not as a replacement for the original archive.

For reproducible analysis, document:

- The original archive path and hash.
- The extraction tool and version, where known.
- The recovery method used.
- Whether extraction completed without errors.
- The output directory and extracted file inventory.
- Any files that could not be extracted or interpreted.

### Important Limitation

The supplied material confirms the presence of an encrypted project archive and a subsequent extracted-content analysis stage. It does not identify the archive format, encryption algorithm, password, recovery technique, or exact extraction commands.

Those details should be added only after confirming them against the challenge artifacts or original technical notes.

## Phase 8 — Project Artifact Analysis

![Project contents](../docs/assets/08-project-contents.png)

The extracted project corpus contains multiple documents, logs, exports, specifications, and the challenge's final evidence container.

This phase moves from recovering files to understanding their investigative significance.

### Artifact Classification

A useful first step is to organize the extracted files by type and purpose.

| Artifact category | Potential investigative value |
|---|---|
| Documents | May contain project context, instructions, or operational records |
| Logs | May support event sequencing and activity correlation |
| Exports | May preserve structured records from another system |
| Specifications | May describe expected system or project behavior |
| Final evidence container | Identified as the artifact used in the challenge's final analysis stage |

These are general interpretations of the categories listed in the supplied walkthrough, not claims about the exact contents of each individual file.

### Correlation Strategy

A structured review should consider:

1. Which artifacts are directly relevant to the reconstructed network session.
2. Whether timestamps, identifiers, or names connect separate files.
3. Whether documents corroborate or contradict the reconstructed content.
4. Whether metadata provides additional context.
5. Whether the final evidence container can be traced to its source.
6. Which conclusions remain uncertain after cross-referencing the available material.

### Avoiding Unsupported Conclusions

The presence of a document, log, or export does not automatically prove a particular event occurred. A defensible conclusion should identify the artifact supporting it and explain how the artifact relates to the claim.

The supplied material does not include a complete extracted-file inventory or the content of the individual documents. Therefore, this report does not attribute specific attacker actions, credentials, or timelines to those files.

## Phase 9 — Liberty Prime Validation

![Liberty Prime](../docs/assets/09-liberty-prime-ui.png)

The challenge uses Liberty Prime, a challenge-specific analysis tool, to inspect the known flag artifact.

The tool represents the final analysis stage in the documented workflow. Its output is relevant to completing the lab, but a tool-generated result should be distinguished from an independently verified forensic conclusion.

### Validation Objectives

- Identify the artifact being examined.
- Confirm that the artifact is the expected challenge evidence container.
- Establish what the tool actually reports.
- Preserve the connection between the result and the source artifact.
- Determine whether the result can be reproduced or corroborated independently.

### Why Independent Verification Matters

A specialized tool may provide a useful abstraction of complex evidence. However, a result can still depend on the tool's assumptions, input selection, implementation, or interpretation.

Where feasible, validate the finding against the original artifact or an independent analysis method.

In an actual incident, the final report should record the source evidence, tool version, relevant processing settings, and any known limitations.

### Challenge-Specific Outcome

The supplied walkthrough identifies Liberty Prime as the tool used to inspect the known flag artifact. The implementation details, internal analysis process, and exact verification output are not included in the source material.

This documentation therefore records the tool's role without claiming independent verification beyond what has been provided.

## Phase 10 — Final Evidence and Result

![Final evidence](../docs/assets/10-flag-redacted-evidence.png)

The final stage presents the evidence associated with challenge completion.

The public portfolio version intentionally redacts the challenge answer to avoid publishing the solution directly.

The investigation sequence ends with artifact analysis and the use of Liberty Prime. The broader forensic lesson is that a final result should remain traceable to its source material and should not be accepted solely because a specialized tool displays it.

### Completion Summary

The documented workflow covers:

- Initial access to the challenge environment.
- Endpoint triage and artifact discovery.
- Identification and prioritization of PCAP files.
- Network evidence reconstruction.
- Recognition of prompt-injection content.
- Encrypted archive investigation.
- Review of extracted project artifacts.
- Challenge-specific final artifact analysis.
- Redaction of the public challenge answer.

**Reporting note:** Completing the challenge and independently proving every possible incident detail are different outcomes. This report documents the supplied completion workflow without claiming a full reconstruction of every event in the simulated environment.

---

## Investigation Chain

The investigation can be summarized as the following evidence flow:

```text
Authorized SSH Access
        |
        v
Endpoint Triage
        |
        v
PCAP Discovery
        |
        v
Candidate Evidence Prioritization
        |
        v
PCAP Reconstruction
        |
        v
Recovered Content Analysis
        |
        v
Prompt-Injection Assessment
        |
        v
Encrypted Archive Investigation
        |
        v
Extracted Project Artifact Analysis
        |
        v
Liberty Prime Artifact Inspection
        |
        v
Challenge Result and Evidence Review
```

### Interpretation

The chain illustrates the order in which the supplied walkthrough progresses through the evidence.

It should not be read as a complete attacker kill chain. It describes the analyst's investigation path, not necessarily the chronological sequence of the original compromise.

## Evidence Handling and Provenance

Evidence provenance is essential to a defensible incident-response investigation. Each conclusion should be traceable to the source artifact and the analysis process that produced it.

### Recommended Evidence Register

For a production-quality investigation, maintain a register such as the following:

| Field | Purpose |
|---|---|
| Evidence ID | Unique identifier for each item |
| Original path | Location where the item was discovered |
| Description | Short description of the artifact |
| Collection time | Time the investigator acquired the item |
| Source timestamps | Relevant timestamps recorded by the source |
| File size | Size of the original artifact |
| SHA-256 hash | Integrity reference for the artifact |
| Acquisition method | How the item was collected |
| Analysis method | Tool or procedure used to examine it |
| Derived artifacts | Files generated from the source evidence |
| Findings | Observations supported by the item |
| Limitations | Known gaps or interpretation constraints |

The source material does not provide these values for the individual challenge artifacts. The table is a recommended reporting structure, not a completed evidence register.

### Original Evidence and Derived Evidence

Maintain a clear distinction between:

- Original PCAP files.
- Reconstructed output produced by the IR assistant.
- The original encrypted archive.
- Extracted files derived from the archive.
- Results produced by Liberty Prime.
- Analyst notes and AI-generated summaries.

Derived files can be valuable, but they should retain an explicit relationship to their source evidence.

### Integrity and Reproducibility

For an actual investigation, cryptographic hashes can help identify whether an artifact has changed between collection and analysis. Hashes alone do not establish authenticity or prove that the source was trustworthy; they support integrity tracking when combined with sound acquisition and handling procedures.

## Key Findings and Security Implications

The supplied walkthrough supports several important investigation themes.

### 1. Network Captures Can Contain Recoverable Evidence

PCAP collections can preserve communications that are useful for reconstructing an incident. A structured prioritization process can reduce the effort required to identify promising candidates.

**Implication:** Network evidence should be inventoried, preserved, and correlated with endpoint artifacts rather than reviewed in isolation.

### 2. Reconstruction Improves Readability but Does Not Replace Source Evidence

The challenge's internal IR assistant provides a readable representation of selected network evidence.

**Implication:** Investigators should preserve source captures and validate reconstructed content when the conclusion is consequential.

### 3. Recovered Content Can Carry Prompt-Injection Attempts

The reconstructed artifact contains a prompt-injection trail, making AI trust boundaries a central investigation concern.

**Implication:** Incident-response systems that use language models must treat recovered text as untrusted input and prevent it from controlling privileged instructions, tools, or actions.

### 4. Archive Recovery Creates Additional Evidence Dependencies

The encrypted project archive leads to a collection of extracted project artifacts.

**Implication:** Archive contents should be tracked as derived evidence, with clear links to the original archive and the extraction process.

### 5. Specialized Tools Require Validation

Liberty Prime is used to inspect the designated challenge artifact.

**Implication:** Tool output should be interpreted in context and corroborated against source evidence whenever possible.

### Finding Limitations

The supplied walkthrough does not establish a specific threat actor, malware family, confirmed data-exfiltration event, exact packet-level attack, or real-world compromise. Those conclusions would require additional direct evidence.

## Defensive Recommendations

The investigation themes suggest several defensive controls for environments that process network evidence, archives, and AI-assisted workflows.

### Network Monitoring and Evidence Preservation

- Maintain centralized collection of relevant network telemetry.
- Establish retention policies that preserve evidence needed for incident response.
- Record capture metadata, source locations, and collection times.
- Use cryptographic hashes to support evidence-integrity tracking.
- Document reconstruction procedures and retain links to original PCAP files.
- Correlate network evidence with endpoint logs and other relevant sources.

### Endpoint Visibility

- Maintain appropriate endpoint logging and monitoring.
- Inventory potentially relevant files during incident triage.
- Preserve suspicious artifacts before performing destructive remediation.
- Correlate file metadata with other evidence sources.
- Apply least-privilege access controls to sensitive project directories and investigation artifacts.
- Document collection and extraction steps for reproducibility.

### Secure Archive Handling

- Restrict archive access to authorized personnel and systems.
- Use approved recovery and extraction tools.
- Preserve original archives and document extraction results.
- Inspect extracted content before opening or executing files.
- Use isolated analysis environments when handling potentially malicious artifacts.
- Record extraction failures and unexpected output rather than silently discarding them.

### AI-Assisted Incident Response

AI-assisted investigation tools should operate under a strict separation between trusted instructions and untrusted evidence.

Recommended controls include:

- Treat all recovered documents, logs, PCAP-derived text, and archive contents as untrusted.
- Keep evidence content separate from system-level instructions.
- Prevent evidence text from granting itself tool permissions or changing investigation scope.
- Require explicit authorization for external actions and sensitive operations.
- Apply least privilege to tool integrations.
- Restrict access to credentials, secrets, and unrelated investigation data.
- Record AI-assisted processing steps and preserve the source material.
- Validate important findings against original evidence.
- Require human review before high-impact response decisions.
- Test the system against prompt injection and other adversarial inputs.

### Evidence Governance

- Define evidence-handling and retention procedures.
- Use unique evidence identifiers and maintain an audit trail.
- Record hashes and analysis metadata.
- Distinguish direct observations from inferences.
- Document known gaps and tool limitations.
- Ensure the final incident report supports independent review.

These controls are general defensive recommendations informed by the investigation themes. They are not claims that West Tech implemented or failed to implement any particular control.

## Lessons Learned

### Evidence Must Be Prioritized, Not Assumed

A large directory of capture files can create unnecessary investigation overhead. Prioritization helps focus effort, but each candidate still requires validation.

### File Size Is a Signal, Not Proof

Size and naming conventions can help identify candidates. They cannot establish maliciousness, relevance, or evidentiary value on their own.

### Reconstruction Must Remain Traceable

Converting packet-level evidence into readable content improves accessibility. The source capture must remain available so the reconstruction can be verified.

### Prompt Injection Is a Forensic Concern

Adversarial instructions may appear inside the very artifacts investigators are required to analyze. AI-assisted workflows must not confuse the content of evidence with trusted instructions.

### Archive Contents Need Provenance

Extracted files should remain traceable to their original archive and extraction process.

### Tool Output Is Not the Same as Independent Proof

A specialized utility can accelerate analysis, but its output should be understood in context and independently checked where feasible.

### Good Reporting Includes Uncertainty

A professional report documents what was observed, what was inferred, what was validated, and what remains unknown. This is more reliable than presenting every intermediate interpretation as established fact.

## Limitations and Validation Notes

This document is based on the supplied ContAInment walkthrough summary and screenshot references.

The following details were not provided and have therefore not been invented:

- Exact shell commands used during the investigation.
- The specific PCAP filename selected for reconstruction.
- Packet numbers, endpoint addresses, protocol details, or session identifiers.
- The exact prompt-injection payload.
- The encrypted archive's format, password, or recovery procedure.
- A complete inventory of extracted project files.
- Cryptographic hashes and evidence timestamps.
- Liberty Prime's internal implementation or detailed output.
- The unredacted challenge answer.

These omissions do not invalidate the documented investigation sequence. They identify where the original technical notes, screenshots, or challenge artifacts would be needed to create a fully reproducible, command-by-command walkthrough.

For a stronger forensic record, add exact commands and tool output only after verifying them against the original challenge evidence.

## Challenge Answer

The actual challenge answer is intentionally redacted in this public portfolio version.

```text
THM{REDACTED_FOR_PUBLICATION}
```

The placeholder above is not the actual challenge answer.

## Conclusion

ContAInment provides a practical framework for exploring incident-response workflows that combine endpoint triage, network evidence reconstruction, archive investigation, and AI-assisted artifact analysis.

The most significant security lesson is the need to preserve evidence provenance and enforce trust boundaries throughout the investigation. Reconstructed content can reveal useful leads, but its interpretation must remain tied to source evidence. Similarly, text recovered from incident artifacts must never gain authority merely because it is formatted as an instruction to an AI system.

A reliable DFIR workflow combines systematic collection, careful prioritization, traceable analysis, independent validation, and transparent reporting of uncertainty.

These principles apply beyond a training lab: they are essential whenever investigators use automation or AI to process potentially adversarial digital evidence.

## References and Resources

- [TryHackMe](https://tryhackme.com/) — Cybersecurity training platform.
- [Wireshark](https://www.wireshark.org/) — Network protocol analysis.
- [NIST Computer Security Incident Handling Guide (SP 800-61 Rev. 2)](https://csrc.nist.gov/pubs/sp/800/61/r2/final) — Incident-response guidance.
- [NIST Guide to Integrating Forensic Techniques into Incident Response (SP 800-86)](https://csrc.nist.gov/pubs/sp/800/86/final) — Digital forensic principles for incident response.

These resources provide additional context for the general DFIR practices discussed in this report. They are not presented as tools necessarily used during the challenge.

## Repository

Source repository: [ContAInment — TryHackMe Walkthrough](https://github.com/anurag-rvnkr1/ContAInment-TryHackMe-Walkthrough)

The repository contains the challenge documentation and associated portfolio material.

## Author

**Anurag Revankar**

Cybersecurity | Digital Forensics and Incident Response | Security Research

*This document is intended for educational purposes and authorized cybersecurity training.*

