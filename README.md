# SOC / SIEM Monitoring Dashboard

## Overview

This project demonstrates the development of a SOC monitoring dashboard in Splunk designed to provide Tier 1 analysts with centralized visibility into suspicious activity across a Windows Active Directory environment.

The dashboard combines multiple detection use cases into a single analyst view, including suspicious authentication activity, potential network reconnaissance, and suspicious PowerShell process execution. Single-value panels provide a quick indication of activity meeting each detection threshold, while supporting investigation tables expose the underlying event details needed for further analysis.

The dashboard was built using telemetry collected from a Windows workstation through Windows Security logs, Windows Filtering Platform events, and Sysmon process creation events. Existing detection logic developed in previous security projects was adapted and integrated into the dashboard to create a more centralized SOC monitoring workflow.

## Dashboard Use Cases

The dashboard provides visibility into three primary security use cases:

- **Suspicious Authentication Activity** — Identifies accounts experiencing five or more failed network logins within a 120-second window using Windows Security Event ID 4625.
- **Potential Network Reconnaissance** — Identifies sources targeting 100 or more unique destination ports within a two-minute window using Windows Filtering Platform Event ID 5152.
- **Suspicious Process Activity** — Identifies PowerShell process creation events containing encoded command parameters using Sysmon Event ID 1.

## Lab Environment

| System | IP Address | Role |
|---|---|---|
| DC01 | 192.168.100.10 | Windows Server 2025 Domain Controller / Splunk Enterprise |
| WS01 | 192.168.100.20 | Windows 10 domain workstation / monitored endpoint |
| KALI01 | 192.168.100.30 | Security testing and attack simulation system |

The environment operates on an isolated VirtualBox network. Security telemetry from WS01 is collected using the Splunk Universal Forwarder and forwarded to Splunk Enterprise on DC01 for investigation, detection, and dashboard monitoring.

## Splunk Investigation

Before building the dashboard, the telemetry supporting each monitoring use case was investigated in Splunk to verify the relevant events, fields, and detection behavior.

### Network Reconnaissance Investigation

Network reconnaissance was generated from KALI01 against WS01 using Nmap. The scan targeted ports 1–1000 on the monitored workstation and generated Windows Filtering Platform Event ID 5152 telemetry for analysis in Splunk.

![Kali Nmap Port Scan](01%20-%20Kali-Nmap-Port-Scan.png)

Analysis of the blocked network traffic identified KALI01 (`192.168.100.30`) as the source associated with 999 unique destination ports.

![Unique Port Scan Analysis](02%20-%20Unique-Port-Scan-Analysis.png)

The activity was further investigated by examining the source address, destination address, and individual destination ports associated with the scan.

![Port Scan Event Investigation](03%20-%20Port-Scan-Event-Investigation.png)

### Suspicious Authentication Investigation

Windows Security Event ID 4625 telemetry was analyzed to identify repeated failed network authentication attempts. The investigation identified five failed login attempts from `192.168.100.30` against the `ryan.admin` account within approximately 28 seconds.

![Suspicious Failed Login Analysis](04%20-%20Suspicious-Failed-Login-Analysis.png)

### Suspicious Process Investigation

Sysmon Event ID 1 process creation telemetry was analyzed for PowerShell executions containing encoded command parameters. Relevant fields including `User`, `Image`, `ParentImage`, and `CommandLine` were retained to provide context for analyst investigation.

![Suspicious Process Activity Analysis](05%20-%20Suspicious%20Process%20Activity%20Analysis.png)

## Dashboard Development

After validating the telemetry supporting each use case, the detections were integrated into a centralized Splunk dashboard. The dashboard was designed with two layers: single-value panels for rapid identification of suspicious activity and detailed tables for further analyst investigation.

### Single-Value Monitoring Panels

Three single-value panels were created to provide an immediate count of events meeting the criteria for each security use case.

#### Suspicious Authentication Activity

This panel identifies accounts with five or more failed network login attempts occurring within a 120-second window using Windows Security Event ID 4625.

![Suspicious Authentication Single Value](01%20-%20Suspicious-Authentication-Single-Value.png)

#### Potential Network Reconnaissance

This panel identifies sources targeting 100 or more unique destination ports within a two-minute window using Windows Filtering Platform Event ID 5152.

![Potential Network Reconnaissance Single Value](02%20-%20Potential-Network-Reconnaissance-Single-Value.png)

#### Suspicious Process Activity

This panel identifies PowerShell process creation events containing encoded command parameters using Sysmon Event ID 1.

![Suspicious Process Activity Single Value](03%20-%20Suspicious-Process-Activity-Single-Value.png)

### Investigation Detail Tables

Detail tables were added beneath the single-value panels to provide analysts with the event context needed to begin investigating activity identified by the dashboard.

#### Suspicious Authentication Activity Details

The authentication table provides the source network address, targeted account, number of failed logins, first and last failure times, and the duration of the failed-login window.

![Suspicious Authentication Activity Details](04%20-%20Suspicious-Authentication-Activity-Details.png)

#### Potential Network Reconnaissance Details

The network reconnaissance table provides the event time, source address, destination address, and number of unique destination ports associated with the detected activity.

![Potential Network Reconnaissance Details](05%20-%20Potential-Network-Reconnaissance-Details.png)

#### Suspicious Process Activity Details

The process activity table provides the event time, host, process image, parent process, command line, and user associated with each detected PowerShell execution.

![Suspicious Process Activity Details](06%20-%20Suspicious-Process-Activity-Details.png)

## Dashboard Workflow

The dashboard was designed to support a simple SOC monitoring and triage workflow. Analysts can first review the single-value panels to quickly determine whether activity has met the defined detection criteria. When suspicious activity is identified, the corresponding detail table provides additional context for investigation.

This structure allows the dashboard to serve as both a high-level monitoring interface and an investigation starting point without requiring analysts to manually run each SPL search.

## Project Outcome

The completed dashboard centralizes three security monitoring use cases into a single Splunk interface:

- Suspicious authentication activity based on repeated failed network logins
- Potential network reconnaissance based on a high number of unique destination ports
- Suspicious PowerShell activity involving encoded command execution

Each use case combines detection logic with an analyst-focused detail view, allowing suspicious activity to be identified and then investigated using relevant event fields.

The project demonstrates how individual detections can be expanded into a centralized monitoring workflow that supports visibility, triage, and investigation within a SOC environment.

## Skills Demonstrated

- Splunk dashboard development
- SPL query development and refinement
- SIEM monitoring
- Windows Security log analysis
- Sysmon process analysis
- Windows Filtering Platform event analysis
- Detection engineering
- Security event triage
- Network reconnaissance analysis
- Authentication monitoring
- Suspicious process investigation
- SOC monitoring workflow design

## Final Project Report

A complete project report documenting the dashboard design, detection logic, investigation process, and project results is available below.

[View the SOC Security Monitoring Dashboard Project Report](SOC_Security_Monitoring_Dashboard_Project_Report.pdf)


