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
