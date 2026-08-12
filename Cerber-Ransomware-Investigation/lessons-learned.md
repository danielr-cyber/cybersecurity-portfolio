# Lessons Learned

## Investigation Lessons

This investigation reinforced the importance of correlating multiple sources of security telemetry rather than relying on a single log source.

### 1. Follow the Attack Chain

The investigation became much clearer when the events were viewed as a connected sequence rather than as isolated alerts.

The attack progressed from:

**Reconnaissance → Brute Force → Valid Account Access → Web Compromise → Payload Delivery → Script Execution → Ransomware → Data Encryption**

Understanding this progression helped connect the August 10 web compromise with the August 24 Cerber ransomware activity.

### 2. Correlate Multiple Data Sources

No single data source provided the complete picture.

* **Sysmon** helped identify process execution and file activity.
* **Suricata** provided network-based security alerts.
* **DNS telemetry** helped identify suspicious domain queries.
* **HTTP telemetry** revealed the download of `mhtr.jpg`.
* **SMB telemetry** helped identify communication between the infected workstation and file server.
* **OSINT** provided additional context for suspicious files, domains, and malware activity.

Combining these sources provided stronger evidence than analyzing any individual log source alone.

### 3. Do Not Trust File Extensions

One of the most important findings was the use of `mhtr.jpg` to disguise the Cerber payload.

The `.jpg` extension initially made the file appear benign, but its context was suspicious because it was downloaded from a malicious domain during the ransomware infection.

This reinforced the importance of investigating the behavior and context surrounding a file rather than relying solely on its filename or extension.

### 4. Verify the Exact Process and Event Type

During the investigation, identifying the correct Sysmon event and process was important.

For example:

* **Sysmon Event ID 1** — Process Creation
* **Sysmon Event ID 2** — File creation time changed
* **Sysmon Event ID 11** — File created

Using the wrong event type could result in missing important evidence or drawing incorrect conclusions.

The investigation also confirmed that the ransomware chain used **`wscript.exe`**, rather than assuming that another scripting interpreter such as `cscript.exe` was responsible.

### 5. Pivoting Is More Effective Than Searching Everything at Once

The investigation was most effective when searches were progressively narrowed.

For example:

**Suricata alert → suspicious host → DNS activity → HTTP traffic → downloaded file → endpoint activity**

This reduced noise and made it easier to establish relationships between events.

### 6. Timing Matters

Timestamps were critical for establishing the sequence of events.

The close relationship between the VBScript execution, the download of `mhtr.jpg`, execution of the ransomware payload, and subsequent file encryption helped establish a coherent infection chain.

Building a timeline also made it possible to connect seemingly unrelated events occurring on different systems.

---

## Detection and Response Improvements

Based on the investigation, several improvements could help an organization detect and respond to similar activity more quickly.

### Endpoint Monitoring

Improve monitoring for suspicious script execution, particularly:

* `wscript.exe` launching scripts from user-writable directories
* Unexpected VBScript execution
* Scripts initiating network connections
* Temporary files executing shortly after script activity

### Network Monitoring

Create detections for:

* Workstations communicating with known malicious domains
* Suspicious downloads from newly identified or low-reputation domains
* Unusual HTTP downloads involving non-executable file extensions
* Unexpected SMB activity between workstations and file servers

### Authentication Monitoring

Monitor for:

* High-volume authentication failures
* Brute-force patterns
* Successful authentication following numerous failed attempts
* Administrative logins from unusual sources

### Backup and Recovery

Maintain regular, tested backups of critical data and ensure backups are protected from ransomware-related access.

Recovery procedures should be tested periodically so that restoration can occur quickly during a ransomware incident.

---

## Personal Takeaways

This investigation improved my understanding of how a SOC analyst can move from an initial alert to a broader incident narrative.

The most important lesson was that individual events often do not tell the entire story. Correlating endpoint, network, DNS, HTTP, and IDS data made it possible to reconstruct the attack and understand how the activity progressed from initial access to ransomware impact.

The investigation also reinforced the importance of understanding **why** a Splunk query is being used rather than simply knowing the syntax. Each query should answer a specific investigative question and provide a logical path to the next pivot.

