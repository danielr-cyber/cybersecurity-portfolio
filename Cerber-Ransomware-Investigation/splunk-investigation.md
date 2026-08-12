# Splunk Investigation

## Investigation Overview

This investigation used Splunk to correlate endpoint, network, DNS, HTTP, and IDS telemetry associated with a multi-stage attack culminating in a Cerber ransomware infection.

The investigation focused on identifying the initial ransomware execution chain, tracing the Cerber payload download, identifying affected systems, and determining the number of files encrypted.

### Primary Data Sources

- Sysmon endpoint telemetry
- Suricata IDS alerts
- DNS telemetry
- HTTP network telemetry
- SMB network activity

### Investigation Approach

The investigation followed a progressive pivoting methodology:

1. Verify available telemetry.
2. Identify relevant security events.
3. Narrow results to the affected host.
4. Extract relevant fields from raw logs.
5. Correlate endpoint and network activity.
6. Identify the ransomware payload.
7. Measure the resulting impact.

## 1. Verify Sysmon Telemetry

### Objective

The first step was to confirm that Sysmon endpoint telemetry was available in the dataset before narrowing the investigation to specific events.

### Query

```spl
index=client1 source="WinEventLog:Microsoft-Windows-Sysmon/Operational"

## 2. Identifying Encrypted Files

### Objective

Determine how many `.txt` files within Bob Smith's Windows user profile were modified during the Cerber ransomware infection.

### Investigation

After verifying that Sysmon telemetry was available, the investigation was narrowed to **Sysmon Event ID 2**, which records changes to file creation timestamps.

```spl
index=client1 source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
| rex max_match=1 "<EventID>(?<EventID>\d+)</EventID>"
| where EventID=2
