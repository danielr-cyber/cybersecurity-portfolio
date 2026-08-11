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
