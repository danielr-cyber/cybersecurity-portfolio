# Multi-Stage Web Compromise & Cerber Ransomware Investigation

## Overview

This project documents a multi-stage incident investigation involving an initial compromise of a public-facing Joomla web server followed by a Cerber ransomware infection affecting internal systems.

The investigation was performed using Splunk, Sysmon, Suricata, network telemetry, and open-source intelligence (OSINT). The analysis traced attacker activity from reconnaissance and brute-force authentication attempts through web server compromise, malicious file activity, ransomware execution, and data encryption.

The investigation focused on reconstructing the attack timeline, identifying affected systems and indicators of compromise (IOCs), correlating activity across multiple log sources, and mapping observed behavior to the MITRE ATT&CK framework.

> **Project Type:** Incident Response / SOC Investigation  
> **Environment:** Security Operations Lab  
> **Primary SIEM:** Splunk  
> **Malware:** Cerber Ransomware

## Scenario

The investigation began with suspicious activity against a public-facing Joomla web server hosting `imreallynotbatman.com`.

On August 10, 2016, an external attacker performed automated reconnaissance and repeated login attempts against the Joomla administrator interface. The attacker successfully obtained valid administrator credentials and gained access to the web application.

The compromised server was subsequently associated with malicious file activity and website defacement.

Approximately two weeks later, on August 24, 2016, a Cerber ransomware infection was identified on an internal workstation. The infection began after a USB device was connected and a malicious VBScript was executed. The script downloaded `mhtr.jpg`, an image file containing the ransomware payload through steganography.

The ransomware subsequently executed `121214.tmp`, encrypted files on the workstation, and communicated with an internal file server over SMB. Additional files on the file server were encrypted.

The investigation therefore covers two major phases of the incident:

1. **Initial compromise — August 10, 2016**
   - Reconnaissance
   - Joomla brute-force attack
   - Valid account compromise
   - Malicious file activity
   - Website defacement

2. **Ransomware deployment — August 24, 2016**
   - USB execution
   - VBScript execution
   - Malicious payload download
   - Cerber execution
   - File encryption
   - SMB communication with the file server

## Analyst Objectives

The primary objective of the investigation was to reconstruct the attack lifecycle, determine the scope and impact of the compromise, and identify indicators that could support containment and remediation.

The investigation focused on answering the following questions:

- How did the attacker initially gain access to the public-facing web server?
- Which accounts, systems, and infrastructure were involved in the initial compromise?
- What malicious files and activity were associated with the compromised web server?
- How did the Cerber ransomware infection begin on the internal workstation?
- Which systems were affected by the ransomware?
- How did the ransomware move from the workstation to the internal file server?
- How many files were affected?
- What indicators of compromise could be extracted for future detection and threat hunting?
- Which MITRE ATT&CK techniques were represented by the observed activity?

## Tools & Data Sources

The investigation relied on multiple telemetry sources to correlate endpoint, network, and security-alert data.

| Tool / Data Source | Purpose |
|---|---|
| **Splunk** | Centralized log analysis, correlation, searching, and timeline reconstruction |
| **Sysmon** | Endpoint process creation, file activity, and process lineage |
| **Suricata** | Network intrusion detection and identification of suspicious traffic |
| **DNS Telemetry** | Identification of suspicious domain queries and correlation with affected hosts |
| **HTTP Telemetry** | Investigation of web requests, downloaded files, source/destination IPs, and timestamps |
| **SMB Telemetry** | Investigation of communication between the infected workstation and internal file server |
| **OSINT / Threat Intelligence** | Validation and contextualization of suspicious domains, files, hashes, and malware activity |
| **MITRE ATT&CK** | Mapping observed attacker behavior to standardized adversary techniques |

## Executive Findings

The investigation identified a multi-stage attack beginning with the compromise of a public-facing Joomla web server and culminating in a Cerber ransomware infection affecting internal systems.

### Initial Compromise

- The attacker originated from `23.22.63.114`.
- Automated reconnaissance activity associated with Acunetix was observed against the public-facing web server.
- The attacker conducted repeated brute-force attempts against the Joomla administrator interface.
- A valid administrator password was successfully identified and used to gain access.
- The compromised website was subsequently defaced with `poisonivy-is-coming-for-you-batman.jpeg`.
- Malicious file activity was identified on the compromised web server, including `3791.exe`.

### Ransomware Infection

Approximately two weeks later, ransomware activity was identified on workstation `we8105desk` (`192.168.250.100`).

- A USB device named `MIRANDA_PRI` was connected to the workstation.
- A malicious VBScript was executed through `wscript.exe`.
- The script downloaded `mhtr.jpg` from the malicious domain `solidaritedeproximite.org`.
- `mhtr.jpg` used a benign-looking `.jpg` extension to conceal the Cerber payload through steganography.
- The ransomware subsequently executed `121214.tmp`.
- Approximately **401 `.txt` files** were encrypted on the workstation.
- The workstation communicated with file server `192.168.250.20` over SMB.
- Approximately **257 PDF files** were encrypted on the file server.

### Overall Assessment

The investigation confirmed unauthorized access to the public-facing web application, malicious activity on the compromised server, and a subsequent ransomware infection affecting internal systems.

The combined evidence demonstrated activity across multiple stages of the attack lifecycle, from reconnaissance and initial access through execution, payload delivery, lateral communication, and data encryption for impact.

## Investigation Timeline

A high-level view of the attack progression:

* **Aug. 10, 2016** — Automated reconnaissance and brute-force activity targeted the Joomla administrator interface for `imreallynotbatman.com`.
* **Aug. 10, 2016** — The attacker successfully authenticated using valid administrator credentials, uploaded `3791.exe`, and defaced the website.
* **Aug. 24, 2016 — 16:42 UTC** — USB device `MIRANDA_PRI` was connected to workstation `we8105desk`.
* **16:43 UTC** — A malicious VBScript was executed through `wscript.exe`, beginning the Cerber infection chain.
* **16:48 UTC** — `mhtr.jpg` was downloaded from `solidaritedeproximite.org`. The image disguised the Cerber payload using steganography.
* **16:48–17:15 UTC** — Cerber executed and encrypted approximately 401 `.txt` files on the workstation.
* **17:15 UTC** — The workstation communicated with file server `192.168.250.20` over SMB, where approximately 257 PDF files were subsequently encrypted.

> **Detailed timeline:** See [incident-timeline.md](incident-timeline.md) for the full event reconstruction and investigation context.


## Indicators of Compromise (IOCs)

The investigation identified indicators associated with the initial web compromise, Cerber ransomware infection, and affected internal systems.

Key indicators included:

* **Attacker IP:** `23.22.63.114`
* **Staging IP:** `40.80.148.42`
* **Target Domain:** `imreallynotbatman.com`
* **Malicious Domain:** `solidaritedeproximite.org`
* **Malicious Files:** `3791.exe`, `mhtr.jpg`, `121214.tmp`
* **Defacement File:** `poisonivy-is-coming-for-you-batman.jpeg`
* **Affected Workstation:** `we8105desk` (`192.168.250.100`)
* **Affected File Server:** `192.168.250.20`

For the complete IOC reference, including hashes, affected systems, and investigative relationships, see **[IOCs](iocs.md)**.

