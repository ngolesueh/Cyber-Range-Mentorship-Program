# Project 7: Log Analysis on Azure Using Microsoft Defender for Endpoint

**Tools used:** Microsoft Defender for Endpoint (MDE), Microsoft Sentinel (Log Analytics workspace), Microsoft Azure, KQL

## Rationale

As an aspiring SOC Analyst, Cybersecurity Analyst, or Security Analyst, understanding how to analyze the logs flowing into any tool responsible for collecting them is a core skill. This project is not only about analyzing logs to detect threats or compromise on an endpoint — it also walks through onboarding an endpoint so that everything performed on it is actually collected in the first place, and then queries those logs with KQL.

## Background: Microsoft Defender for Endpoint

Microsoft Defender for Endpoint (MDE) is an enterprise endpoint security platform designed to help organizations prevent, detect, investigate, and respond to advanced threats and cyberattacks across their network. MDE ships with built-in behavioral sensors embedded directly in Windows, macOS, Linux, iOS, and Android that collect and process behavioral signals and system telemetry in real time. It also feeds threat intelligence that helps identify attacker tools, techniques, and procedures (TTPs), mapping detected activity directly to the MITRE ATT&CK framework.

### Defender vs. Sentinel

MDE is primarily an Extended Detection and Response (XDR) platform that protects native Microsoft 365 and Azure environments. Microsoft Sentinel, by contrast, is a cloud-native SIEM and SOAR platform built for organization-wide visibility across cloud and on-premises systems — including non-Microsoft environments like AWS and GCP. In short: Defender is an EDR/XDR platform scoped to Microsoft 365 and Azure, while Sentinel is a cloud-native SIEM/SOAR platform for a mixed multi-cloud/on-prem estate.

![Log flow in the Microsoft Azure SOC ecosystem](./screenshots/01-log-flow-overview.png)

### Key Integrations

Organizations typically use Defender to automatically stop threats at the endpoint and identity layer, while streaming Defender logs into Microsoft Sentinel to correlate them against network firewalls, multi-cloud platforms, and third-party SaaS applications. Microsoft integrates Defender and Sentinel within a single portal, letting SOC analysts manage alerts, incidents, and hunting queries from one dashboard — the Defender and Sentinel data sources shown below are connected, so the same device tables can be queried from either surface.

![Defender and Sentinel integration in a single portal](./screenshots/02-defender-sentinel-integration.png)

## Objectives

1. Onboard an endpoint onto Microsoft Defender for Endpoint to collect logs on the activity performed on it.
2. Perform triage actions on an indicator of compromise — isolation, investigation, and reinstatement when nothing serious is found.
3. Use KQL to query the logs for specific indicators of compromise, or simply to review the actions performed on the endpoint.

## Tools

- **Microsoft Defender for Endpoint** — collects all the logs analyzed in this project.
- **Microsoft Azure** — hosts the Windows 11 virtual machine that acts as the endpoint under test.

---

## Phase 1: VM Creation and Onboarding

1. Log into the Microsoft Azure portal and create a Windows 11 virtual machine.
2. Log into the virtual machine (via RDP or Azure Bastion).
3. To onboard the VM: **Microsoft Defender → System Settings → Endpoints → Device Management → Onboarding**. Onboarding requires admin rights on the tenant; since I didn't have that, I obtained the Windows 10/11 MDE package download link from the platform admin for this exercise. The link is copied into Notepad rather than downloaded on a personal machine first — moving a downloaded package onto the VM gets flagged as malicious by the VM's security controls and won't copy over.
4. In the VM, open Microsoft Edge, paste the link, and download the MDE onboarding package.
5. Run the package — it opens a command line, walks through onboarding, and displays a success message once complete.
6. Confirm the device shows up in MDE by name. It can take a few minutes to appear. It can also be found under **Assets → Devices**, searching by VM name (I saw two devices with the same name in my tenant — one was a leftover from an earlier run of this same exercise; the correct one is identified by creation date).

Once the VM appears in MDE, everything performed on it going forward is collected, and can be queried for a range of investigative purposes.

![Creating the Windows 11 VM in Azure (1)](./screenshots/03-create-vm-1.png)
![Creating the Windows 11 VM in Azure (2)](./screenshots/04-create-vm-2.png)
![Creating the Windows 11 VM in Azure (3)](./screenshots/05-create-vm-3.png)
![VM deployment complete](./screenshots/06-vm-created.png)
![Logging into the VM](./screenshots/07-vm-login-page.png)
![Connecting to the VM via Azure Bastion](./screenshots/08-vm-login-bastion.png)
![Logged into the VM](./screenshots/09-logged-into-vm.png)
![Checking Windows Defender Firewall status on the VM](./screenshots/10-defender-firewall-check.png)
![MDE system settings](./screenshots/11-mde-system-setting.png)
![Endpoints onboarding menu in MDE system settings](./screenshots/12-endpoint-onboarding-menu.png)
![Menu path to onboard a device in MDE](./screenshots/13-onboard-vm-menu.png)
![MDE onboarding package download link](./screenshots/14-mde-package-download-link.png)
![Opening Edge in the VM to download the MDE package (1)](./screenshots/15-opening-edge-download-1.png)
![Opening Edge in the VM to download the MDE package (2)](./screenshots/16-opening-edge-download-2.png)
![MDE package download complete](./screenshots/17-package-download-complete.png)
![MDE package unzipped](./screenshots/18-package-unzipped.png)
![Running the MDE onboarding package (1)](./screenshots/19-running-package-1.png)
![Running the MDE onboarding package (2)](./screenshots/20-running-package-2.png)
![Running the MDE onboarding package (3)](./screenshots/21-running-package-3.png)
![Running the MDE onboarding package (4)](./screenshots/22-running-package-4.png)
![MDE onboarding package run complete](./screenshots/23-running-package-complete.png)
![Onboarding confirmed successful from logs](./screenshots/24-onboarding-successful.png)
![Logged into the MDE portal](./screenshots/25-mde-portal.png)
![MDE navigation menu](./screenshots/26-mde-menu-pane.png)
![Finding the onboarded VM in MDE (1)](./screenshots/27-find-onboarded-vm-1.png)
![Finding the onboarded VM in MDE (2)](./screenshots/28-find-onboarded-vm-2.png)
![Onboarded VM confirmed via the Assets menu](./screenshots/29-found-onboarded-vm-assets.png)

---

## Phase 2: Log Generation and Query

To generate logs from the VM, I performed a set of activities on it and then used KQL in MDE's **Advanced Hunting** page to query the logs for those activities over a given time window.

![MDE's device log tables](./screenshots/30-log-types-menu.png)
![Advanced Hunting KQL query editor](./screenshots/31-advanced-hunting-menu.png)
![Generating log activity on the VM](./screenshots/32-log-generating-activity.png)

### Use Case 1 — DeviceLogonEvents

**Goal:** Generate an authentication event and see it recorded.

**Task:** Connect to the virtual machine via RDP (or Azure Bastion, which also uses RDP).

**Query:**

```kql
DeviceLogonEvents
| where DeviceName == "vm-name"
| where Timestamp > ago(45m)
| project Timestamp, ActionType, AccountName, LogonType, RemoteIP, InitiatingProcessFileName
| sort by Timestamp desc
```

**Expect:** A `LogonSuccess` row for the account. Console/Unlock shows `LogonType` `Interactive`; RDP shows `RemoteInteractive` with the source machine in `RemoteIP`. The query surfaces the VM's logon activity — time, account name, initiating process, and logon type.

![DeviceLogonEvents query results (1)](./screenshots/33-logon-events-results-1.png)
![DeviceLogonEvents query results (2)](./screenshots/34-logon-events-results-2.png)

### Use Case 2 — DeviceProcessEvents

**Goal:** Spawn a process and capture its command line and parent.

**Task:** Open `cmd` and run a harmless command that creates a clear parent-child chain:

```
cmd.exe /c whoami
```

For a slightly richer tree, launch it from PowerShell to get a grandparent relationship to observe:

```
powershell.exe -Command cmd /c whoami
```

**Query:**

```kql
DeviceProcessEvents
| where DeviceName == "vm-name"
| where Timestamp > ago(45m)
| where FileName in~ ("cmd.exe", "whoami.exe", "powershell.exe")
| project Timestamp, FileName, ProcessCommandLine,
          InitiatingProcessFileName, InitiatingProcessParentFileName, AccountName
| sort by Timestamp desc
```

**Expect:** Rows for `cmd.exe` and `whoami.exe` with `ProcessCommandLine` populated, `InitiatingProcessFileName` set to the parent (`powershell.exe` or `cmd.exe`), and the account in `AccountName`.

![Running the process-chain commands on the VM](./screenshots/35-process-event-task.png)

**Troubleshooting note:** My first few passes at this query threw syntax and semantic errors — a typo'd `Timestaamp` and a lowercase `initiatingProcessParentFileName` where KQL expected `InitiatingProcessParentFileName`. KQL column names are case-sensitive, and this was a good reminder to read the error's `Token`/`Line`/`Position` fields closely rather than re-guessing.

![KQL syntax error — misspelled column name](./screenshots/36-process-event-error-1.png)
![KQL semantic error — column name not resolved](./screenshots/37-process-event-error-2.png)
![Same semantic error after a partial fix](./screenshots/38-process-event-error-3.png)
![Semantic error on the next unresolved column](./screenshots/39-process-event-error-4.png)

Once the column names were corrected, the query returned the full process chain — `whoami.exe` and `cmd.exe` spawned under `cmd.exe`/`powershell.exe`, alongside other legitimate MDE sensor process activity (`MsSense.exe`, `senseir.exe`) captured in the same window.

![DeviceProcessEvents query results (1)](./screenshots/40-process-event-results-1.png)
![DeviceProcessEvents query results (2)](./screenshots/41-process-event-results-2.png)

### Use Case 3 — DeviceNetworkEvents

**Goal:** Make an outbound connection and see the destination logged.

**Task:** From PowerShell, make a plain HTTPS request to a benign, easily recognizable destination so it stands out in the results:

```powershell
Invoke-WebRequest https://github.com/ngolesueh/Automation/blob/main/wireshark-winpcap-force-remove.ps1 -UseBasicParsing
```

**Query:**

```kql
DeviceNetworkEvents
| where DeviceName == "vm-name"
| where Timestamp > ago(45m)
| where InitiatingProcessFileName == "powershell.exe"
| project Timestamp, ActionType, RemoteIP, RemotePort, RemoteUrl,
          InitiatingProcessFileName
| sort by Timestamp desc
```

**Expect:** A `ConnectionSuccess` row on `RemotePort` 443, `RemoteUrl` pointing at the target host, attributed to `powershell.exe` — the same shape as an outbound C2 connection, but against a benign destination.

Without `-UseBasicParsing`, PowerShell's `Invoke-WebRequest` refuses to run because it would parse (and potentially execute) script content embedded in the page, and cancels the request:

![PowerShell blocking the request over script-parsing risk](./screenshots/42-network-event-blocked.png)

Re-running with `-UseBasicParsing` completes the request successfully (`StatusCode 200`):

![Successful outbound HTTPS request with -UseBasicParsing](./screenshots/43-network-event-results.png)

### Use Case 4 — DeviceFileEvents

**Goal:** Create a file in a staging-style location and capture the write.

**Task:** Write a harmless file into the Temp directory:

```
echo benign-lab-test > C:\Windows\Temp\labtest.txt
```

To also get a hash recorded, drop a copy of an existing executable:

```
copy C:\Windows\System32\notepad.exe C:\Windows\Temp\labtest.exe
```

**Query:**

```kql
DeviceFileEvents
| where DeviceName == "vm-name"
| where Timestamp > ago(30m)
| where FolderPath has @"\Temp\"
| where FileName startswith "labtest"
| project Timestamp, ActionType, FileName, FolderPath, SHA256,
          InitiatingProcessFileName
| sort by Timestamp desc
```

**Expect:** A `FileCreated` row for `labtest.txt` / `labtest.exe` under `C:\Windows\Temp\`. The `.exe` copy carries a `SHA256` hash; the initiating process is the shell that ran the copy. Delete the files afterward to clean up.

![Writing labtest.txt to the Temp directory](./screenshots/44-file-event-task.png)
![Writing and copying labtest files, verified in File Explorer](./screenshots/45-file-event-task-verify.png)
![labtest file visible in the Temp folder](./screenshots/46-file-event-explorer.png)
![DeviceFileEvents query results (1)](./screenshots/47-file-event-results-1.png)
![DeviceFileEvents query results (2)](./screenshots/48-file-event-results-2.png)

### Use Case 5 — DeviceRegistryEvents

**Goal:** Write an autostart (Run key) value and observe the registry change.

**Task:** Add a Run key value pointing at a benign program, mirroring a persistence technique without any malware, then remove it:

```
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Run" /v LabHealthCheck /t REG_SZ /d "C:\Windows\System32\notepad.exe" /f

:: clean up when done
reg delete "HKCU\Software\Microsoft\Windows\CurrentVersion\Run" /v LabHealthCheck /f
```

**Query:**

```kql
DeviceRegistryEvents
| where DeviceName == "vm-name"
| where Timestamp > ago(30m)
| where RegistryKey has @"\CurrentVersion\Run"
| where ActionType in ("RegistryValueSet", "RegistryKeyCreated")
| project Timestamp, ActionType, RegistryValueName, RegistryValueData,
          InitiatingProcessFileName
| sort by Timestamp desc
```

**Expect:** A `RegistryValueSet` row with `RegistryValueName` `LabHealthCheck` and `RegistryValueData` pointing at `notepad.exe`, written by `reg.exe`.

![Adding the LabHealthCheck Run key value](./screenshots/49-registry-event-task-add.png)
![Adding, then deleting, the LabHealthCheck Run key value](./screenshots/50-registry-event-task-add-delete.png)

**Observation:** In the same query window, MDE also picked up a `RegistryValueSet` event on the same `...CurrentVersion\Run` key written by `msedge.exe` (a Microsoft Edge auto-launch entry) — a reminder that the Run key is genuinely busy on a live Windows box, and a real investigation has to separate the change under test from legitimate background noise, not just look for "a row."

![DeviceRegistryEvents query results](./screenshots/51-registry-event-results.png)

### Use Case 6 — DeviceEvents

**Goal:** Trigger a structured sensor event — a scheduled task creation.

`DeviceEvents` is the catch-all table for typed sensor events without a dedicated table of their own. Creating a scheduled task raises a clean `ScheduledTaskCreated` action here (a service install would raise `ServiceInstalled` the same way).

**Task:** Create a harmless daily task, then delete it:

```
schtasks /create /tn LabUpdaterTask /tr C:\Windows\System32\notepad.exe /sc daily /st 09:00 /f

:: clean up when done
schtasks /delete /tn LabUpdaterTask /f
```

**Query:**

```kql
DeviceEvents
| where DeviceName == "vm-name"
| where Timestamp > ago(30m)
| where ActionType == "ScheduledTaskCreated"
| project Timestamp, ActionType, AdditionalFields,
          InitiatingProcessFileName, InitiatingProcessAccountName
| sort by Timestamp desc
```

**Expect:** A `ScheduledTaskCreated` row with the task name (`LabUpdaterTask`) inside the `AdditionalFields` JSON, initiated by `schtasks.exe` under the logged-in account. The same `schtasks` command also lands in `DeviceProcessEvents` — a good example of one action getting recorded two different ways, from two different vantage points.

![Creating and deleting the scheduled task](./screenshots/52-scheduled-task-create-delete.png)

---

## Key Takeaways

- Onboarding is the prerequisite for everything else — no device in MDE means no telemetry to query, regardless of how good the KQL is.
- Each device table (`DeviceLogonEvents`, `DeviceProcessEvents`, `DeviceNetworkEvents`, `DeviceFileEvents`, `DeviceRegistryEvents`, `DeviceEvents`) maps to a distinct vantage point on the same endpoint, and real investigations usually need more than one of them together to build the full picture of an action.
- KQL is case-sensitive on column names, and its error messages (syntax vs. semantic, with the exact token/line/position) are precise enough to debug from directly rather than guessing.
- Background noise is real: a live Windows VM writes to common attacker-relevant locations (like the Run key) on its own, so filtering has to be specific enough to isolate the activity under investigation.
- `DeviceEvents` is the catch-all for sensor activity that doesn't have its own dedicated table, and the same underlying action (e.g., a `schtasks` command) can show up in more than one table from different angles.

---

Back to [portfolio home](../../README.md).
