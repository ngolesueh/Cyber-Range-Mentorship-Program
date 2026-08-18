# Project 1: Unauthenticated Scan on a Windows 11 Pro VM (Azure) Using Tenable

**Tools used:** Tenable, Azure Cloud Environment

## Rationale

Before layering on credentials or deeper configuration, it's useful to see what a vulnerability scanner can observe about a system with zero inside access — the same vantage point an external attacker would have. This project establishes that baseline using an unauthenticated Tenable scan against a freshly deployed Windows 11 VM.

## Goals / Objectives

Perform an unauthenticated (black-box) vulnerability scan against a Windows 11 Pro VM in Azure using Tenable, and observe the type and depth of findings an unauthenticated scan is able to surface.

## Assumptions

- I have access to Azure and Tenable, as well as Tenable's vulnerability management guide.
- I am familiar with VM creation and configuration on Azure.

## Phase 1: VM Setup

1. Create a Windows 11 Pro VM in Azure.
2. Log into the VM.
3. Disable the Windows Firewall for the Domain, Private, and Public profiles. (If I had created and attached a Network Security Group (NSG) to the VM, I would have made sure to add a rule allowing all inbound traffic from the Tenable Scan Engine by including its IP address in the rule. I used the VM's private IP address because the scanner and VM are in the same network environment — if they were on separate networks, I would have used the VM's public IP address instead.)

![Azure VM interface](./screenshots/01-azure-vm-interface.png)

![VM deployment status](./screenshots/02-vm-deployment-status.png)

![Logging into the VM](./screenshots/03-login-into-vm.png)

![Disabling the Windows Firewall](./screenshots/04-disabling-firewall.png)

## Phase 2: Tenable Scan Configuration & Results

1. Log into Tenable.
2. Select a scan type — for this project, a Basic Network Scan.
3. Under Basic, configure the scan by giving it a name, choosing the scan type (Internal), and setting the target to the VM's private IP (a public IP would be used if the scan engine were external to the network).
4. Under Discovery, select the custom scan type, activate Ping the Remote Host, and activate Use Fast Network Discovery. No credentials are added for this scan.
5. Save & Launch.

![Tenable login page](./screenshots/05-tenable-login.png)

![Basic Network Scan configuration](./screenshots/06-scan-config.png)

![Unauthenticated scan results](./screenshots/07-scan-results.png)

## Observations

- Take note of how long the scan takes to complete. If it finishes within just a couple of minutes, there may be an issue with the configuration or VM settings — most often, this means the VM firewall wasn't actually disabled. A real scan takes a while, so this is a good point to take a break.
- My scan took about 10 minutes to complete. Clicking See All Details shows what the scan found. It's worth noting that an unauthenticated scan has very low access, so it performs a surface-level assessment compared to authenticated or agent-based scans, which have much higher access and perform an in-depth assessment. As a result, the vulnerabilities found tend to be medium or low severity.
- Unauthenticated scans are mostly useful for identifying externally facing vulnerabilities — the ones visible to the public or to an attacker without credentials.

---

Back to [portfolio home](../../README.md). Next: [Project 2 — Authenticated Scan on Windows](../02-authenticated-scan-windows/README.md)
