---
layout: default
title: "Sysmon FileCreate Event ID 11"
permalink: /solo-purple-teaming/sysmon-filecreate-event-id-11/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Sysmon FileCreate Event ID 11</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **1. Introduction to Event ID 11**

- **What it is:** Sysmon Event ID 11 logs **file creation** operations when a file is created or overwritten.
- **Why it’s important:**
    - Helps monitor *auto-start* locations (e.g., Startup folder).
    - Captures files in **temporary** and **Downloads** directories, which are common malware drop locations during initial infection.

---

### **2. Sysmon Filter Conditions**

Sysmon allows filtering events based on specific **conditions** applied to event fields.

Key condition types:

| **Condition Type** | **Meaning** | **Example** |
| --- | --- | --- |
| `is` | Exact match | Image is `C:\Windows\System32\cmd.exe` |
| `is not` | Exact mismatch | Image is not `cmd.exe` |
| `contains` | Field contains a given substring | Target file contains `Downloads` |
| `contains any` | Field contains any of multiple substrings | `cmd.exe`, `powershell.exe` |
| `contains all` | Field contains all listed substrings | — |
| `begin with` | Field starts with a specified string | Path begins with `C:\Windows\System32\drivers` |
| `end with` | Field ends with a specified string | — |
| `match` | Match using a regular expression | — |

---

### **3. Modifying Sysmon Config for Event ID 11**

**Goal:** Capture files created in high-risk directories.

1. Locate **Event ID 11** section in your Sysmon configuration.
2. Change it to `include` rules.
3. Add a `<TargetFilename>` filter:
    - **Condition:** `contains`
    - **Value:** `Downloads`
4. Duplicate and adjust the filter for:
    - `AppData` (condition: `contains`)
    - `C:\Windows\System32\drivers` (condition: `begin with`)
    - `C:\Windows\SysWOW64\drivers` (condition: `begin with`)

---

### **4. Applying the Configuration**

1. Save your modified Sysmon config file (`sysmonconfig.xml`).
2. From an **Administrator Command Prompt**, run:
    
    ```
    r
    CopyEdit
    sysmon -c sysmonconfig.xml
    
    ```
    
3. Ensure configuration validation and update succeed.

---

### **5. Testing the Filter**

**Objective:** Verify Event ID 11 logs a file creation in the Downloads folder.

1. **Open Event Viewer**
    - Path: `Applications and Services Logs > Microsoft > Windows > Sysmon > Operational`
2. **Filter logs** for Event ID `11`.
3. **Clear the log** to capture only new events.
4. **Trigger a test event**:
    - Download a file from the attack host’s web staging directory.
    - Example: Access `http://192.168.100.101:8000` and download `goodbye_AMSI_1.exe` into `Downloads`.
    - The file may trigger SmartScreen scanning before saving.

---

### **6. Reviewing the Event**

- In **Event Viewer**, confirm:
    - File creation entry for `goodbye_AMSI_1.exe`
    - Presence of **Zone.Identifier** (Mark of the Web), indicating the file came from the internet.

---

### **7. Verifying in Wazuh**

1. In Wazuh’s dashboard, go to **Explore > Discover**.
2. Change the index pattern to `archives`.
3. Query for:
    - Event ID = `11`
    - `data.win.TargetFilename` contains `AMSI`
4. Confirm event details:
    - File path
    - Mark of the Web attribute

---

### **8. Additional Notes**

- **Mark of the Web** is a valuable indicator for files downloaded from the internet.
- Research **Sysmon Event ID 15** to detect **Alternate Data Streams** and Mark of the Web.
- Before continuing, ensure your Wazuh manager is **logging file creations in the Downloads folder**.

---

### **Next Step**

In the next lecture, you will explore **PowerShell script block logging** to detect malicious script execution related to initial access payloads.

</section>
</div>
