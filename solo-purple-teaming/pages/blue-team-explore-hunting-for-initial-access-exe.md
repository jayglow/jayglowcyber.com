---
layout: default
title: "Blue Team Explore - Hunting For Initial Access Exe"
permalink: /solo-purple-teaming/blue-team-explore-hunting-for-initial-access-exe/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Blue Team Explore - Hunting For Initial Access Exe</h1>
</section>
<section class="spt-content">

# Blue Team Explore  - Hunting For Initial Access Execution

Owner: Mike Sterrett

## **1. Recap of Previous Lecture**

- In the last lecture, we examined the **execute assembly** command:
    - Spawns a sacrificial process.
    - Injects a CLR loader.
    - Reflectively loads a .NET assembly to execute.
- In our attack path:
    - **PowerShell** hosts our **Apollo agent**.
    - PowerShell accessed **smartscreen.exe**, granting it **0x1fffff** (full access rights).
    - SmartScreen created a **unique named pipe**.

---

### **2. This Lecture’s Goal**

- Step back in the kill chain to look for **initial access indicators**.
- Focus on:
    1. Relationship between **Run Dialog** and the malicious PowerShell command.
    2. **Registry events** related to running the command from Run Dialog.

---

### **3. Updating Sysmon Configuration**

1. Add a **filter** in `process create` events:
    - Only capture where `Image` ends with `.exe`.
2. Add an additional **destination port (8000)**:
    - This captures connections to the **staging server** that downloads Apollo.
3. Save and apply config:
    
    ```bash
    sysmon64 -c base_sysmon_config.xml
    
    ```
    
4. Verify configuration update.

---

### **4. Understanding the Run Dialog and Registry Storage**

- **Run Dialog** shows command history (auto-populated).
- Stores up to **25 commands** in:
    
    ```
    HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU
    
    ```
    
    - Keys: `a` (first command), `b` (second), etc.
    - `MRUList` value defines display order.

---

### **5. Lab Steps – Observing & Clearing RunMRU**

1. Open **Regedit** and navigate to **RunMRU**.
2. Note the malicious PowerShell command in key `a`.
3. Copy the command, delete all RunMRU entries.
4. Open **Run Dialog** – it’s empty now.
5. Paste attack command → execute → check **Mythic** for callback.
6. If no callback, re-run payload and clear registry again.

---

### **6. Verifying in Wazuh**

1. Go to **Explore → Discover** in Wazuh.
2. Change **index pattern** to `wazuh-archives`.
3. Search for:
    
    ```
    data.win.event.image:*powershell.exe AND data.win.system.eventID:3
    
    ```
    
4. Add **Destination IP** column to verify connections to **attack infrastructure**.
5. Optionally, add **Destination Port** column (expect ports 80 & 8000).

---

### **7. Pivoting to Process Relationships**

- Add **Process GUID** as a column → filter events by GUID.
- Check Event IDs linked to PowerShell:
    - **12**: Registry create/delete.
    - **3**: Network connect.
    - **7**: Image load.
    - **13**: Registry value modification.
- Identify **parent process** of PowerShell:
    - Filter `eventID:1` → parent is **explorer.exe** (Run Dialog runs under Explorer context).

---

### **8. Tracing Parent Process Activity**

1. Replace filter to search by **parent process GUID**.
2. Find related registry events (**12 & 13**).
3. Add **Details** column → shows registry value set.
4. Add **Target Object** column → shows registry path.
5. Locate **RunMRU** key:
    - Example: `RunMRU\a` contains the malicious PowerShell command.

---

### **9. Mapping to IOC Diagram**

- Update diagram:
    - **explorer.exe** (clean) → launches **powershell.exe** (malicious).
    - **powershell.exe** → runs Apollo loader, connects to C2.
    - **explorer.exe** sets **RunMRU** registry keys.
    - Initial access triggered by **Run Dialog**.

---

### **10. Student Checklist Before Moving On**

✅ See **RunMRU** key storing malicious PowerShell command.

✅ Verify **network connections** to C2 (ports 80 & 8000).

✅ Map IOC chain in diagram:

- Run Dialog → explorer.exe → powershell.exe → smartscreen.exe → C2.

---

### **Next Step**

- In the **next lecture**, we’ll begin the **Blue Team Trace Phase** and perform **reverse engineering** of captured artifacts.

</section>
</div>
