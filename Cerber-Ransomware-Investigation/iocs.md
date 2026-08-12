# Indicators of Compromise (IOCs)

## Overview

This document contains the technical indicators identified during the investigation of the multi-stage web compromise and Cerber ransomware infection.

The indicators are grouped by category to support future detection, threat hunting, and incident response.

---

## Attacker Infrastructure

| Indicator                   | Type         | Context                                                                                         |
| --------------------------- | ------------ | ----------------------------------------------------------------------------------------------- |
| `23.22.63.114`              | IPv4 Address | External source associated with brute-force activity against the Joomla administrator interface |
| `40.80.148.42`              | IPv4 Address | Staging infrastructure associated with the attack                                               |
| `imreallynotbatman.com`     | Domain       | Public-facing Joomla application targeted during the initial compromise                         |
| `solidaritedeproximite.org` | Domain       | Malicious domain associated with the Cerber payload download                                    |

---

## Malware Indicators

| Indicator                                                          | Type     | Context                                                       |
| ------------------------------------------------------------------ | -------- | ------------------------------------------------------------- |
| `3791.exe`                                                         | Filename | Malicious executable uploaded to the compromised web server   |
| `AAE3F5A29935E6ABCC2C2754D12A9AF0`                                 | MD5      | Hash associated with `3791.exe`                               |
| `MirandaTateScreensaver.scr.exe`                                   | Filename | Malware sample identified during threat intelligence research |
| `9709473ab351387aab9e816eff3910b9f28a7a70202e250ed46dba8f820f34a8` | SHA-256  | Hash associated with `MirandaTateScreensaver.scr.exe`         |

---

## Ransomware Indicators

| Indicator     | Type       | Context                                                                                                          |
| ------------- | ---------- | ---------------------------------------------------------------------------------------------------------------- |
| `mhtr.jpg`    | Filename   | Image file downloaded during the Cerber infection; used to disguise the ransomware payload through steganography |
| `121214.tmp`  | Filename   | Temporary payload executed during the ransomware infection                                                       |
| `MIRANDA_PRI` | USB Device | USB device connected to the infected workstation immediately before the ransomware execution chain               |

---

## Affected Systems

| System       | IP Address        | Role                 | Impact                                   |
| ------------ | ----------------- | -------------------- | ---------------------------------------- |
| `we8105desk` | `192.168.250.100` | Internal workstation | Approximately 401 `.txt` files encrypted |
| File Server  | `192.168.250.20`  | Internal file server | Approximately 257 PDF files encrypted    |

---

## Defacement Indicator

| Indicator                                 | Type     | Context                                                              |
| ----------------------------------------- | -------- | -------------------------------------------------------------------- |
| `poisonivy-is-coming-for-you-batman.jpeg` | Filename | Defacement image associated with the August 10 web server compromise |

---

## Key Investigative Relationships

The following relationships were established during the investigation:

* `23.22.63.114` → brute-force activity → Joomla administrator interface
* Successful credentials → compromised Joomla administrator account
* Compromised web server → `3791.exe` uploaded
* Compromised website → `poisonivy-is-coming-for-you-batman.jpeg` defacement
* `we8105desk (192.168.250.100)` → `wscript.exe` → malicious VBScript
* Malicious VBScript → `solidaritedeproximite.org` → `mhtr.jpg`
* `mhtr.jpg` → steganographic Cerber payload
* Cerber → `121214.tmp` → file encryption
* `we8105desk` → SMB → `192.168.250.20`
* File server → approximately 257 PDF files encrypted

---

## IOC Usage

These indicators could support defensive activities such as:

* SIEM searches and correlation
* Network blocking and monitoring
* DNS monitoring
* Endpoint detection and response
* Threat hunting
* Malware investigation
* Retrospective log analysis

> **Note:** These indicators should be investigated in context with timestamps, endpoint activity, and network connections rather than treated as proof of compromise by themselves.

