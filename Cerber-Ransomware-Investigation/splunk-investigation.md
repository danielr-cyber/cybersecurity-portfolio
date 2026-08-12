# Splunk Investigation

## Investigation Overview

This investigation used Splunk to correlate endpoint, network, DNS, HTTP, and IDS telemetry associated with a multi-stage attack culminating in a Cerber ransomware infection.

The investigation focused on identifying the ransomware execution chain, tracing the Cerber payload download, identifying affected systems, and determining the number of files encrypted.

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

---

## 1. Verify Sysmon Telemetry

### Objective

Confirm that Sysmon logs are present in the Splunk dataset and that endpoint telemetry is being ingested correctly.

### Query

    index=client1 source="WinEventLog:Microsoft-Windows-Sysmon/Operational"

### Why This Query Was Used

The investigation began by verifying that Sysmon telemetry was available before narrowing the search to specific events.

This established a baseline view of the endpoint data available in the `client1` index.

### Investigation Takeaway

Starting with a broad telemetry check helps prevent investigators from building searches against data that is missing or incorrectly ingested.

---

## 2. Identify File Metadata Modification Events

### Objective

Identify Sysmon events associated with changes to file creation timestamps.

Cerber ransomware can modify file metadata during the encryption process. **Sysmon Event ID 2** records changes to file creation time.

### Query

    index=client1 source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
    | rex max_match=1 "<EventID>(?<EventID>\d+)</EventID>"
    | where EventID=2

### Why This Query Was Used

The search was narrowed to **Event ID 2** because the investigation was looking for file metadata changes associated with ransomware activity.

This distinction was important because **Event ID 11** represents file creation, while **Event ID 2** represents a change to file creation time.

### Investigation Takeaway

Selecting the correct event type is critical during incident investigation. Using Event ID 11 instead of Event ID 2 could lead to incomplete or misleading results.

---

## 3. Extract and Filter Target Files

### Objective

Identify `.txt` files located within Bob Smith's Windows user profile.

### Extract the Target Filename

    | rex field=_raw "<Data Name='TargetFilename'>(?<TargetFilename>[^<]+)</Data>"

The `TargetFilename` field was extracted from the raw Sysmon event data because it contained the path of the affected file.

### Filter to Bob Smith's `.txt` Files

    | where like(TargetFilename, "C:\\Users\\bob.smith.WAYNECORPINC\\%.txt")

The search was narrowed to `.txt` files within Bob Smith's user profile.

The wildcard allows the search to include files within subdirectories of the profile.

### Investigation Takeaway

Filtering by both **file type** and **user profile path** reduced unrelated events and focused the investigation on the files most relevant to the ransomware activity.

---

## 4. Count Unique Encrypted Files

### Objective

Determine how many unique `.txt` files were affected.

### Query

    | stats dc(TargetFilename) as unique_txt_files_encrypted

The `dc()` function performs a distinct count, ensuring that duplicate events referencing the same file are not counted multiple times.

### Why `dc()` Was Used

Using `stats count` would count every matching event rather than every unique file.

Because multiple events can reference the same file, `dc(TargetFilename)` provides a more accurate measurement of the number of affected files.

### Result

The investigation identified:

**401 unique `.txt` files**

### Investigation Takeaway

The result provided a measurable indicator of ransomware impact on the workstation.

---

## 5. Final Query

The individual investigation steps were combined into a single Splunk query:

    index=client1 source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
    | rex max_match=1 "<EventID>(?<EventID>\d+)</EventID>"
    | where EventID=2
    | rex field=_raw "<Data Name='TargetFilename'>(?<TargetFilename>[^<]+)</Data>"
    | where like(TargetFilename, "C:\\Users\\bob.smith.WAYNECORPINC\\%.txt")
    | stats dc(TargetFilename) as unique_txt_files_encrypted

### Final Result

**401 unique `.txt` files were identified as affected by the Cerber ransomware activity.**

---

## 6. Identify the Ransomware Payload

### Objective

Determine which downloaded file contained the Cerber cryptor code.

The investigation previously identified `mhtr.jpg` as a suspicious file downloaded during the ransomware infection. Because the file used a `.jpg` extension, the HTTP activity required additional investigation rather than assuming it was a legitimate image.

### HTTP Query

    index=client1 sourcetype="stream:http" uri="*mhtr.jpg*"

### Result

The HTTP telemetry showed that `mhtr.jpg` was downloaded from the malicious infrastructure during the ransomware infection.

The download occurred at approximately:

**2016-08-24 16:48:13 UTC**

### Investigation Takeaway

The `.jpg` extension did not indicate that the file was harmless. In this case, the file was associated with the Cerber payload and used **steganography** to conceal malicious content inside an apparently benign image file.

---

## 7. Trace the Script Execution

### Objective

Identify the script responsible for initiating the ransomware download.

The previous investigation established that `mhtr.jpg` was downloaded through a script launched using `wscript.exe`.

### Query

    index=client1 source="WinEventLog:Microsoft-Windows-Sysmon/Operational" wscript.exe EventCode=1
    | table _time, Image, CommandLine

### Why This Query Was Used

`EventCode=1` represents **Process Creation** events in Sysmon.

The search was narrowed to `wscript.exe` because the investigation identified Windows Script Host as part of the execution chain.

The `table` command was used to display the most relevant fields in an easier-to-read format.

### Investigation Takeaway

This pivot connected the endpoint process activity with the network activity used to download `mhtr.jpg`.

---

## 8. Correlate Endpoint and Network Activity

The investigation connected several events into a single execution chain:

1. USB device `MIRANDA_PRI` was connected to workstation `we8105desk`.
2. A malicious VBScript was executed through `wscript.exe`.
3. The script initiated network activity.
4. `mhtr.jpg` was downloaded from `solidaritedeproximite.org`.
5. The downloaded file contained the Cerber payload through steganography.
6. `121214.tmp` was executed.
7. Files on the workstation were encrypted.
8. The workstation communicated with file server `192.168.250.20`.
9. Additional files on the file server were encrypted.

### Investigation Takeaway

Correlating endpoint and network telemetry provided stronger evidence than analyzing either source independently.

This demonstrated the value of **pivoting between telemetry sources during incident response**.

---

## 9. Investigation Lessons

Several important SOC investigation techniques were demonstrated during this investigation:

- Begin with a broad search to verify available telemetry.
- Identify the correct event type before filtering.
- Extract fields when important information is stored inside raw event data.
- Narrow searches using known hosts, users, file paths, and indicators.
- Pivot between endpoint, DNS, HTTP, and IDS telemetry.
- Use distinct counts when measuring unique affected entities.
- Do not assume a file is benign based solely on its extension.
- Correlate timestamps across multiple data sources to reconstruct an attack sequence.

### Key SOC Skill

**Don't investigate individual logs in isolation. Build the story by pivoting from one piece of evidence to the next.**

The Cerber investigation demonstrated this workflow:

**Telemetry → Event → Host → Process → Network Connection → Payload → Impact**
