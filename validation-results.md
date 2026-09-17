# Validation Results

## 1. SIEM Data Ingestion Validation

The Windows endpoint logs were successfully forwarded from the Splunk Universal Forwarder to Splunk Enterprise.

### Validation

* Splunk Universal Forwarder service: **Running**
* Forwarding destination: **Splunk Enterprise on TCP 9997**
* Dedicated Splunk index: **soc_windows**
* Windows Event Logs: **Received**
* Sysmon Logs: **Received**
* PowerShell Operational Logs: **Received**

### SPL Query

```spl
index=soc_windows
| stats count by sourcetype
| sort -count
```

This query was used to confirm that multiple Windows log sources were successfully ingested into the `soc_windows` index.

---

## 2. Sysmon Process Creation Validation

Sysmon Event ID 1 was validated to confirm process creation telemetry.

### SPL Query

```spl
index=soc_windows EventCode=1
| table _time host ComputerName EventCode Image CommandLine ParentImage ParentCommandLine User
| head 20
```

Controlled commands such as `whoami` and `hostname` were executed on the Windows endpoint to generate process creation events.

### Result

The corresponding process creation events were successfully observed in Splunk.

---

## 3. Sysmon Network Connection Validation

Sysmon Event ID 3 was validated to confirm network connection telemetry.

### SPL Query

```spl
index=soc_windows sourcetype=WinEventLog:Sysmon EventCode=3
```

### Result

Network connection events containing source/destination information, ports, protocol, process information, and related endpoint details were successfully received.

---

## 4. PowerShell Log Validation

PowerShell Operational logs were tested by executing controlled PowerShell commands.

### Test Commands

```powershell
powershell.exe -NoProfile -Command "Get-Date"
```

```powershell
powershell.exe -NoProfile -Command "Get-Process"
```

### SPL Query

```spl
index=soc_windows sourcetype=*PowerShell*
```

### Result

PowerShell activity was successfully captured and searchable in Splunk.

---

## 5. Windows Event Log Validation

Windows Security, System, and Application event logs were checked to confirm successful ingestion.

### SPL Query

```spl
index=soc_windows
| stats count by sourcetype
```

### Result

The expected Windows Event Log sourcetypes were observed in the SIEM.

---

## 6. Event Volume Validation

Daily event volume was reviewed to confirm continuous log ingestion.

### SPL Query

```spl
index=soc_windows
| timechart span=1d count
```

### Result

The time-series results demonstrated that events were being ingested into Splunk over time.


## Conclusion

The validation confirmed that the Windows endpoint telemetry was successfully collected, forwarded through the Splunk Universal Forwarder, ingested by Splunk Enterprise, and made searchable within the `soc_windows` index.

The validated telemetry provides a foundation for subsequent log analysis, threat-hunting exercises, and SOC investigation activities.
