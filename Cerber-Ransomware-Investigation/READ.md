# Multi-Stage Web Compromise & Cerber Ransomware Investigation

## Overview

This project documents a multi-stage incident investigation involving an initial compromise of a public-facing Joomla web server followed by a Cerber ransomware infection affecting internal systems.

The investigation was performed using Splunk, Sysmon, Suricata, network telemetry, and open-source intelligence (OSINT). The analysis traced attacker activity from reconnaissance and brute-force authentication attempts through web server compromise, malicious file activity, ransomware execution, and data encryption.

The investigation focused on reconstructing the attack timeline, identifying affected systems and indicators of compromise (IOCs), correlating activity across multiple log sources, and mapping observed behavior to the MITRE ATT&CK framework.

> **Project Type:** Incident Response / SOC Investigation  
> **Environment:** Security Operations Lab  
> **Primary SIEM:** Splunk  
> **Malware:** Cerber Ransomware
