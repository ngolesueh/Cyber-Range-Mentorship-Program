# Cyber Range Mentorship Program

Documenting my hands-on journey through the Cyber Range Mentorship Program — real-world security projects built and executed end-to-end, from planning through stakeholder buy-in to technical remediation.

## About

This repository is a portfolio of the projects I've completed during the program, organized into tracks. The **Vulnerability Management** track is a numbered skill-building sequence: each project builds the specific vulnerability-management skills (unauthenticated vs. authenticated scanning, scan templates, compliance auditing, agent-based monitoring) that come together in the final capstone project, where those skills are applied to stand up a complete vulnerability management program. The **Log Analysis & Detection** track is a separate skill line focused on onboarding endpoints for telemetry collection and querying that telemetry with KQL to investigate activity. The **Threat Hunting** track applies those skills to full-scale hunts: reconstructing a real attack chain from raw telemetry, with no alert to start from, and reporting the findings to both technical and executive audiences.

## Vulnerability Management Track

| # | Project | Description | Skills / Tools |
|---|---|---|---|
| 1 | [Unauthenticated Scan on Windows](./projects/01-unauthenticated-scan-windows/README.md) | Baseline unauthenticated Tenable scan against a Windows 11 Azure VM. | Tenable, Azure VMs |
| 2 | [Authenticated Scan on Windows](./projects/02-authenticated-scan-windows/README.md) | Credentialed Tenable scan against the same Windows 11 VM, compared against Project 1. | Tenable, Azure VMs, PowerShell |
| 3 | [Unauthenticated Scan on Linux](./projects/03-unauthenticated-scan-linux/README.md) | Baseline unauthenticated Tenable scan against an Ubuntu Azure VM. | Tenable, Azure VMs |
| 4 | [Authenticated Scan on Linux](./projects/04-authenticated-scan-linux/README.md) | Credentialed Tenable scan against the Linux VM via SSH/root. | Tenable, Azure VMs, BASH |
| 5 | [Scan Templates & DISA STIG](./projects/05-scan-template-disa-stig/README.md) | Building a reusable scan template and running a DISA STIG compliance audit against an intentionally vulnerable VM. | Tenable, Azure VMs |
| 6 | [Agent-Based Monitoring](./projects/06-agent-based-monitoring/README.md) | Deploying a Tenable/Nessus agent for continuous, network-independent endpoint monitoring. | Tenable, Azure VMs |
| 🏁 | [Vulnerability Management Program Implementation (Capstone)](./Vulnerability-Management-Project.md) | End-to-end simulation of standing up a vulnerability management program — from drafting policy and securing stakeholder buy-in, through scanning, prioritization, and a full remediation cycle that cut vulnerabilities by 81%. | Tenable, Azure VMs, Nessus, PowerShell, BASH |

### Skills Demonstrated

- Unauthenticated and authenticated vulnerability scanning across Windows and Linux
- Scan template creation and compliance auditing (DISA STIG)
- Agent-based / network-independent endpoint monitoring
- Vulnerability management policy design and stakeholder negotiation
- Remediation planning and prioritization (risk-based)
- Change Control Board (CAB) process and rollback planning
- Scripting for remediation (PowerShell, BASH)
- Reporting on program outcomes and metrics

## Log Analysis & Detection Track

| # | Project | Description | Skills / Tools |
|---|---|---|---|
| 7 | [Log Analysis on Azure Using Microsoft Defender for Endpoint](./projects/07-log-analysis-mde-azure/README.md) | Onboarded a Windows 11 Azure VM to Microsoft Defender for Endpoint, used KQL across six device-telemetry use cases to investigate endpoint activity, then isolated the device from the network and collected a forensic investigation package to simulate full triage of a suspected compromise. | Microsoft Defender for Endpoint, Microsoft Sentinel, Microsoft Azure, KQL |

### Skills Demonstrated

- Endpoint onboarding to Microsoft Defender for Endpoint
- KQL query authoring and debugging (Advanced Hunting)
- Cross-table log investigation (logon, process, network, file, registry, and scheduled-task events)
- Distinguishing investigation-relevant activity from legitimate background noise
- Understanding of Defender/Sentinel integration and the broader Microsoft Azure SOC log flow
- Device isolation for network containment of a suspected compromised endpoint, with reversible release once cleared
- Forensic evidence collection via MDE Investigation Packages, with response actions tracked through the Action Center

## Threat Hunting Track

| # | Project | Description | Skills / Tools |
|---|---|---|---|
| 8 | [Threat Hunt — TideGlass (Agentic LLM-Driven Data Theft)](./projects/08-threat-hunt-tideglass/README.md) | Reconstructed a 52-minute, AI-agent-driven intrusion from raw telemetry — from a marimo kernel RCE through IMDS credential theft, IP-rotating Secrets Manager enumeration, SSH lateral movement, and exfiltration of a 2.8M-row customer table. Solved 28/28 flags, mapped the chain to MITRE ATT&CK and ATLAS, and wrote both a technical incident report and a plain-language executive briefing. | Microsoft Sentinel, KQL, AWS CloudTrail, MITRE ATT&CK, MITRE ATLAS |
| 9 | [Threat Hunt — Harborlight (Akira-Style Ransomware)](./projects/09-threat-hunt-harborlight/README.md) | Rebuilt a full-domain ransomware intrusion at a small dental practice from raw Sysmon and Windows Security logs, with no alert queue: phishing lure, UAC bypass, Print Spooler injection, LSASS and NTDS.dit credential theft, hub-and-spoke lateral movement, rclone exfiltration to MEGA, log clearing, a Domain Admin backdoor and `.akira` encryption, and proved the C2 was still active after encryption. Perfect score (46/46 flags, 4,190/4,190) on a Hard hunt, with technical and plain-language reports. | Microsoft Sentinel, KQL, Sysmon, Windows Security Logs, MITRE ATT&CK |

### Skills Demonstrated

- Hypothesis-driven threat hunting across application, host, cloud, database, and AI-agent telemetry
- Cross-table KQL correlation to rebuild a full kill chain and timeline
- Separating attacker activity from legitimate noise (process lineage, source-IP + secret pairing, account and destination comparison)
- Cloud attack analysis: IMDS credential theft, Secrets Manager abuse, IP-rotation evasion
- Attributing activity as human-driven vs. human-tasked autonomous (LLM agent) execution
- MITRE ATT&CK and MITRE ATLAS mapping, IOC documentation, and telemetry-gap analysis
- Ransomware kill-chain reconstruction on Windows/Active Directory from Sysmon and Security events (injection, LSASS/NTDS dumping, WMI, log clearing, backdoor accounts)
- Incident reporting for both technical and executive audiences

## About Me

DIVINE EPIE NGOL ESUEH ([@ngolesueh](https://github.com/ngolesueh))
