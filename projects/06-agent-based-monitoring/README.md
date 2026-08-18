# Project 6: Agent-Based Monitoring on a Windows 11 VM (Azure) Using Tenable

**Tools used:** Tenable, Azure Cloud Environment

## Rationale

Traditional network vulnerability scans assume all target endpoints sit within a unified local perimeter or network. In modern enterprise environments with remote or distributed workforces, traditional network-based scanning falls short. Agent-based monitoring ensures continuous visibility and active security assessment for off-network and remote endpoints, without relying on network proximity to a scan engine.

## Goals / Objectives

- Deploy distributed monitoring by configuring and installing a Tenable/Nessus agent on a cloud-hosted Windows 11 Azure VM.
- Enable remote endpoint visibility by establishing persistent security monitoring for an off-network host.
- Automate scan triggers by configuring an agent group and scan policy within the Tenable console to assess the endpoint without a network-based scan engine.
- Validate the security posture by running an agent-driven vulnerability assessment and reviewing the results directly from the agent scan.

## Assumptions

- I have access to Azure and Tenable, as well as Tenable's vulnerability management guide.
- I am familiar with VM creation and configuration on Azure.

## Phase 1: VM Setup & Intentional Vulnerabilities

1. Create a Windows 11 Pro VM in Azure.
2. Log into the VM.
3. Disable the Windows Firewall for the Domain, Private, and Public profiles. (If I had created and attached a Network Security Group (NSG) to the VM, I would have made sure to add a rule allowing all inbound traffic from the Tenable Scan Engine by including its IP address in the rule. I used the VM's private IP address because the scanner and VM are in the same network environment.)
4. Intentionally introduced vulnerabilities on the VM for the agent scan to discover: created/enabled an administrator account, set a blank password with no expiration, added that administrator account to the Administrators group, and enabled the guest account and added it to the Administrators group without a password.

*Screenshot placeholder — VM setup / intentional vulnerabilities for the agent-based scan.*

## Phase 2: Agent Deployment & Scan

Unlike Projects 1–5, this project doesn't point a scan engine at the VM over the network — instead, the VM runs its own local agent that reports back to Tenable directly.

1. In Tenable, generate/locate the linking key for the agent group the VM should join.
2. On the Windows 11 VM, download the Nessus/Tenable Agent installer and run it, providing the linking key so the agent registers itself to my Tenable account.
3. Confirm the agent shows a Linked status in Tenable's Sensors → Agents inventory once installation completes.
4. Add the agent to an agent group, then create an agent scan policy (Basic Agent Scan) targeting that group.
5. Launch the agent scan and monitor its status from the Tenable console — agent scans run locally on the endpoint rather than being driven remotely, so there's no target IP or credential set to configure.
6. Review the scan results and diagnostic/agent logs once the scan reports back.

*Screenshot placeholder — Nessus/Tenable agent installation and linking.*

*Screenshot placeholder — agent showing as Linked in the Tenable console.*

*Screenshot placeholder — agent scan results.*

## Observations

- Agent-based scanning flips the model used in Projects 1–5: instead of a scan engine reaching out to the target over the network (which requires the target to be reachable, firewalls opened, and credentials supplied), the agent runs locally on the endpoint and reports its findings back to Tenable on its own schedule. This is what makes it suitable for remote or off-network devices that a network scanner could never reach directly.
- Because the agent has local access to the host by design, it doesn't need the same credential and firewall configuration that Projects 2, 4, and 5 required — the trade-off is the extra step of installing and maintaining agent software on every endpoint.
- Placeholder: once the actual scan results are in, add the specific findings here — how they compare to the network-based scans in Project 5, given the same intentionally-introduced vulnerabilities.

This page documents the intended workflow for the agent-based project; the screenshots and final scan results above are placeholders to be replaced with the actual walkthrough images.

---

Back: [Project 5 — Scan Templates & DISA STIG](../05-scan-template-disa-stig/README.md). Portfolio [home](../../README.md). Next: [Capstone — Vulnerability Management Program Implementation](../../Vulnerability-Management-Project.md)
