# MITRE ATT&CK Mapping

The following MITRE ATT&CK techniques were identified during the investigation.

| Technique | ID | Evidence |
|---|---|---|
| Brute Force | T1110 | The attacker performed repeated password attempts against the Joomla administrator login page and successfully discovered valid credentials. |
| Valid Accounts | T1078 | The attacker successfully authenticated to the Joomla administrator account using valid credentials. |
| Exploitation for Client Execution / Web Application Compromise | T1190 | The attacker gained unauthorized access to the public-facing Joomla application and uploaded malicious content. |
| Ingress Tool Transfer | T1105 | The attacker transferred malicious files, including `3791.exe`, to the compromised web server. |
| Command and Scripting Interpreter: Visual Basic | T1059.005 | A malicious VBScript was executed on the workstation using `wscript.exe` during the Cerber infection. |
| Obfuscated Files or Information | T1027 | The Cerber payload was disguised as the image file `mhtr.jpg`, using steganography to conceal malicious content. |
| Data Encrypted for Impact | T1486 | Cerber encrypted approximately 401 `.txt` files on the workstation and approximately 257 PDF files on the file server. |
| SMB/Windows Admin Shares | T1021.002 | The infected workstation communicated with the internal file server over SMB during the ransomware activity. |
| Defacement | T1491 | The compromised web server was defaced using the image `poisonivy-is-coming-for-you-batman.jpeg`. |

## Key Takeaway

The attack demonstrated a progression from external reconnaissance and credential attacks to web server compromise, malware delivery, internal execution, and ransomware impact.

Mapping the activity to MITRE ATT&CK helps organize the investigation into recognizable attacker behaviors and provides useful context for detection and response.
