---
layout: default
title: "Blue Team Explore - Enabling Sysmon Event IDs 12,"
permalink: /solo-purple-teaming/blue-team-explore-enabling-sysmon-event-ids-12/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Blue Team Explore - Enabling Sysmon Event IDs 12,</h1>
</section>
<section class="spt-content">

# Blue Team Explore - Enabling Sysmon Event IDs 12, 13 & 14

Owner: Mike Sterrett

## **Overview**

In this exercise, we will enable **Sysmon** event IDs **12**, **13**, and **14** to monitor Windows registry changes. These events are critical for detecting suspicious activity—particularly attacker actions involving persistence or execution via the Windows registry.

For **Attack Path Level One**, we are specifically interested in monitoring the **Run MRU** registry key, which records commands executed from the **Windows Run dialog** (e.g., `cmd.exe`, `powershell.exe`). Detecting modifications to this key can reveal attacker activity early in the intrusion.

---

### **Sysmon Registry Events**

- **Event ID 12 – Registry Object Create/Delete**
    - Logs when registry keys or values are created or deleted.
    - Useful for detecting new persistence mechanisms in **Run MRU** or autostart locations.
- **Event ID 13 – Registry Value Set**
    - Logs when an existing registry value is modified.
    - For the **Run MRU** key, this shows the exact command entered in the Run prompt.
    - **Most important** for Attack Path Level One detection.
- **Event ID 14 – Registry Object Rename**
    - Logs when registry keys are renamed.
    - Useful for catching malware that attempts to rename keys for obfuscation.

💡 **Tip:** Sysmon abbreviates root registry hives:

- `HKCU` → **HKEY_CURRENT_USER**
- `HKLM` → **HKEY_LOCAL_MACHINE**

---

### **Lab Steps**

### **Step 1 – Open the Sysmon Configuration**

1. Log in to the **Assumed Breach** host.
2. Open your **base Sysmon configuration file**.
3. Scroll down to the section for **Event IDs 12, 13, and 14**.

### **Step 2 – Enable the Events**

1. Locate the `<Include>` tag for these events.
2. Change the empty `<Include>` to `<Exclude>` and **leave it empty** to ensure *no registry events are excluded*.
3. Save the configuration file.

---

### **Step 3 – Apply the Sysmon Config**

1. Open **Command Prompt** as Administrator.
2. Enter your admin credentials when prompted.
3. Change directory to the `Downloads` folder:
    
    ```powershell
    cd C:\Users\<username>\Downloads
    
    ```
    
4. Apply the updated Sysmon config:
    
    ```powershell
    sysmon64.exe -c basicsmon_sysmonconfig.xml
    
    ```
    
5. Verify that the configuration file was **validated and updated**.

---

### **Step 4 – Verify in Wazuh**

1. Open **Wazuh**.
2. Navigate to **Explore → Discover**.
3. Change the index to **archives**.
4. Search for:
    
    ```
    data.win.system.eventID:13
    
    ```
    
    - Confirm **Event ID 13** entries are appearing.
5. Repeat the search for **Event IDs 12 and 14**:
    - Event 12 entries should appear if registry keys have been created or deleted.
    - Event 14 may not appear yet unless a registry rename event has occurred.

---

### **Validation**

- Ensure **Event ID 13** logs are present—this is the key event for Run MRU monitoring.
- If 12 and 13 are working, 14 should also be captured when applicable.
- Before moving on, verify you see these events in Wazuh.

---

### **Homework**

- Read the provided research article about the **Run MRU** registry key and how attackers can abuse it.
- Be prepared for the **next lecture**, where we will hunt for **malicious registry events** and analyze their relationship to **Attack Path Level One**.

</section>
</div>
