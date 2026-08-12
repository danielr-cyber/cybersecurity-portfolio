# Incident Timeline

## Investigation Timeline

All timestamps are represented in UTC.

| Time                       | Event                                                                                                                            | Significance                                                                                      |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| **2016-08-10 21:45:21**    | Attacker IP `23.22.63.114` began repeated login attempts against the Joomla administrator interface for `imreallynotbatman.com`. | Beginning of the observed initial access activity.                                                |
| **2016-08-10**             | Automated reconnaissance associated with Acunetix was observed against the web server.                                           | Indicates pre-compromise reconnaissance and vulnerability discovery.                              |
| **2016-08-10**             | The attacker attempted hundreds of passwords, including `12345678`.                                                              | Evidence of a brute-force attack against the administrator account.                               |
| **2016-08-10**             | Password `batman` was successfully used to authenticate to the Joomla administrator account.                                     | Successful use of valid credentials established unauthorized access.                              |
| **2016-08-10 21:52:47**    | `3791.exe` was uploaded to the compromised web server.                                                                           | Indicates malicious file transfer following web application compromise.                           |
| **2016-08-10**             | The compromised website was defaced with `poisonivy-is-coming-for-you-batman.jpeg`.                                              | Demonstrates attacker control of the public-facing application and potential reputational impact. |
| **2016-08-24 16:42**       | USB device `MIRANDA_PRI` was connected to workstation `we8105desk`.                                                              | Preceded the observed Cerber infection activity.                                                  |
| **2016-08-24 16:43**       | A malicious VBScript was executed through `wscript.exe`.                                                                         | Initiated the ransomware infection chain.                                                         |
| **2016-08-24 16:48:12**    | HTTP activity associated with the Cerber payload was observed.                                                                   | Indicates payload delivery during the infection sequence.                                         |
| **2016-08-24 16:48:13**    | `mhtr.jpg` was downloaded from `solidaritedeproximite.org`.                                                                      | Malicious payload disguised as an image and associated with steganography.                        |
| **2016-08-24 16:48**       | `121214.tmp` executed on the workstation.                                                                                        | Ransomware execution followed payload delivery.                                                   |
| **2016-08-24 16:48–17:15** | Cerber encrypted files on workstation `192.168.250.100`.                                                                         | Demonstrates data encryption for impact.                                                          |
| **2016-08-24 17:15**       | Workstation communicated with file server `192.168.250.20` over SMB.                                                             | Indicates ransomware-related activity involving an internal file server.                          |
| **2016-08-24 17:15**       | Approximately 257 PDF files were identified as encrypted on the file server.                                                     | Demonstrates additional impact beyond the original workstation.                                   |

## Attack Progression

### Phase 1 — Reconnaissance and Initial Access

The attacker performed automated reconnaissance against the public-facing Joomla server before beginning repeated authentication attempts.

The successful use of the `batman` password against the administrator account established unauthorized access to the web application.

### Phase 2 — Web Server Compromise

Following successful authentication, the attacker uploaded `3791.exe` and subsequently defaced the website using `poisonivy-is-coming-for-you-batman.jpeg`.

This demonstrated that the attacker had gained sufficient control of the web application to modify content and introduce malicious files.

### Phase 3 — Ransomware Deployment

Approximately two weeks later, activity on workstation `we8105desk` showed the connection of the `MIRANDA_PRI` USB device followed by execution of a VBScript through `wscript.exe`.

The script downloaded `mhtr.jpg` from `solidaritedeproximite.org`.

### Phase 4 — Payload Execution and Encryption

The `.jpg` extension initially made `mhtr.jpg` appear to be a benign image. Investigation determined that it was associated with the Cerber payload and used steganography to conceal malicious content.

The ransomware subsequently executed `121214.tmp` and began encrypting files.

### Phase 5 — Internal Impact

The infected workstation communicated with the internal file server `192.168.250.20` over SMB.

Additional files on the file server were encrypted, expanding the impact beyond the original workstation.

## Investigation Takeaway

The timeline demonstrates how seemingly separate events can form a single attack narrative when endpoint, network, DNS, HTTP, and IDS telemetry are correlated.

The incident progressed from reconnaissance and credential attacks to web application compromise, followed later by ransomware execution and data encryption for impact.

