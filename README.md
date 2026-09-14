# SIEM Log Pipeline Engineering & SIEM Architecture

A hands-on cybersecurity project focused on building an end-to-end Windows log ingestion pipeline using **Splunk Enterprise, Splunk Universal Forwarder, Sysmon, and Windows Event Logs**.

The goal was to collect security-relevant telemetry from a Windows endpoint, forward it to a centralized SIEM, verify ingestion, and perform basic security event analysis using Splunk Search Processing Language (SPL).

---

## Project Objective

* Build a practical SIEM environment
* Collect Windows security telemetry
* Deploy and configure Sysmon
* Forward endpoint logs using Splunk Universal Forwarder
* Centralize logs in Splunk Enterprise
* Verify successful log ingestion
* Search and analyze security events using SPL

---

## Architecture

```text
Windows 10 Endpoint
        │
        ├── Windows Security Logs
        ├── Windows System Logs
        ├── Windows Application Logs
        ├── Sysmon
        └── PowerShell Operational Logs
                │
                ▼
      Splunk Universal Forwarder
                │
                │ TCP 9997
                ▼
        Splunk Enterprise
                │
                ▼
          soc_windows Index
                │
                ▼
          SPL Analysis
```

---

## Technologies Used

| Technology                 | Purpose                               |
| -------------------------- | ------------------------------------- |
| Splunk Enterprise          | Centralized SIEM and log analysis     |
| Splunk Universal Forwarder | Endpoint log forwarding               |
| Sysmon                     | Detailed Windows endpoint telemetry   |
| Windows Event Logs         | Security and system events            |
| PowerShell                 | PowerShell activity monitoring        |
| VirtualBox                 | Virtualized lab environment           |
| SPL                        | Security event searching and analysis |

---

## Lab Environment

### Windows Endpoint

* Windows 10 Virtual Machine
* VirtualBox
* Host-only network
* Example endpoint IP: `192.168.56.101`

### Splunk Host

* Windows host machine
* Splunk Enterprise
* Receiving port: `TCP 9997`

### Splunk Index

```text
soc_windows
```

---

# Implementation

## 1. Splunk Enterprise Configuration

Splunk Enterprise was configured as the centralized SIEM.

A dedicated receiving port was enabled:

```text
TCP 9997
```

The endpoint Universal Forwarder was configured to forward logs to the Splunk Enterprise host.

A dedicated index was created:

```text
soc_windows
```

---

## 2. Sysmon Deployment

Sysmon was installed on the Windows endpoint to provide enhanced endpoint visibility.

Installation:

```cmd
Sysmon64.exe -i
```

The Sysmon configuration was then applied using a security-focused configuration file.

```cmd
Sysmon64.exe -c sysmonconfig.xml
```

Sysmon service status was verified:

```cmd
sc query Sysmon64
```

Important telemetry included:

* Process Creation — Event ID 1
* Network Connection — Event ID 3
* Registry Activity — Event IDs 12 and 13

---

## 3. Windows Log Collection

Splunk Universal Forwarder was configured to collect:

```text
Security
System
Application
Microsoft-Windows-Sysmon/Operational
Microsoft-Windows-PowerShell/Operational
```

All selected logs were sent to the:

```text
soc_windows
```

index.

---

## 4. Universal Forwarder Configuration

The Universal Forwarder was configured on the Windows endpoint.

The forwarding destination was:

```text
<SPLUNK_SERVER>:9997
```

Forwarder configuration was verified using Splunk's diagnostic tools.

Example:

```cmd
splunk btool inputs list -debug
```

After configuration changes, the Universal Forwarder service was restarted.

---

# Log Ingestion Verification

The Splunk index was searched to verify that events were successfully received.

```spl
index=soc_windows
```

Sourcetypes were then analyzed:

```spl
index=soc_windows
| stats count by sourcetype
| sort -count
```

This helped verify that multiple Windows log sources were reaching Splunk.

---

# Security Event Analysis

## Process Creation

Sysmon Event ID 1 was used to investigate process creation.

```spl
index=soc_windows EventCode=1
| table _time host ComputerName EventCode Image CommandLine ParentImage ParentCommandLine User
| head 20
```

This provides visibility into:

* Executed processes
* Command lines
* Parent processes
* Users
* Endpoint names
* Process execution time

---

## Network Connections

Sysmon Event ID 3 was used to investigate network connections.

```spl
index=soc_windows sourcetype=WinEventLog:Sysmon EventCode=3
```

Relevant fields include:

* Source IP
* Destination IP
* Source Port
* Destination Port
* Protocol
* Process ID
* Process Image
* User
* Computer Name

---

## PowerShell Activity

PowerShell Operational logs were searched to verify PowerShell telemetry.

```spl
index=soc_windows sourcetype=WinEventLog:PowerShell
```

Controlled PowerShell commands were executed on the endpoint and the resulting events were searched in Splunk.

---

# Controlled Validation

Controlled commands were executed to generate observable endpoint activity.

Example:

```cmd
whoami
```

```cmd
hostname
```

PowerShell:

```powershell
powershell -NoProfile -Command "Get-Date"
```

```powershell
powershell -NoProfile -Command "Get-Process"
```

The resulting telemetry was verified inside Splunk.

---

# Event Volume Monitoring

Daily event volume was reviewed using:

```spl
index=soc_windows
| timechart span=1d count
```

This provides a simple view of the amount of telemetry being ingested over time.

---

# Evidence

The `screenshots/` directory contains selected screenshots demonstrating:

* Splunk receiving configuration
* Universal Forwarder status
* Sysmon deployment
* Sysmon process events
* Sysmon network events
* PowerShell events
* SPL search results

---

# Security Considerations

No passwords, API keys, authentication tokens, or production data are included in this repository.

Environment-specific information should be sanitized before publishing publicly.

---

# Skills Demonstrated

* SIEM Architecture
* Windows Log Collection
* Splunk Enterprise
* Splunk Universal Forwarder
* Sysmon
* Windows Event Logs
* PowerShell Logging
* SPL
* Endpoint Visibility
* Security Event Analysis
* Basic SOC Monitoring

---

# Future Work

The next phase of the lab will focus on practical threat-hunting and detection activities, including:

* Failed Logon — Event ID 4625
* Successful Logon — Event ID 4624
* Process Creation — Sysmon Event ID 1
* PowerShell Activity
* File Creation
* File Modification
* Network Connection — Sysmon Event ID 3
* Registry Activity
* Suspicious Parent → Child Process
* Account Activity

---

## Author

**Lawswin**

Cybersecurity | SOC Analyst | SIEM | Threat Detection
