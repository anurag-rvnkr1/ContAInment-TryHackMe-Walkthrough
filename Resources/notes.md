# ContAInment — Technical Notes

## Challenge

- Room: ContAInment
- Theme: incident response / digital forensics
- Core artifacts: PCAP sessions, reassembled data, encrypted project archive
- Public flag: intentionally redacted

## Investigation Flow

```text
SSH
  ↓
Host triage
  ↓
find *.pcap
  ↓
Prioritize candidate sessions
  ↓
PCAP reassembly
  ↓
Inspect reconstructed text
  ↓
Recover encrypted project archive
  ↓
Analyze extracted artifacts
  ↓
Validate with Liberty Prime
```

## Important Paths

```text
/home/o.deer/Documents/pcap_dumps/
/home/o.deer/qwen-output/
/dev/shm/home/o.deer/westtech_projects/
```

## Defender Notes

- Preserve original evidence before transformation.
- Record the exact source path for every derived artifact.
- Treat prompt content in recovered files as untrusted data.
- Independently verify AI-generated findings.
- Restrict forensic tooling permissions and file access.
