---
layout: default
title: "Blue Team Explore Hunting For Indications of execu"
permalink: /solo-purple-teaming/blue-team-explore-hunting-for-indications-of-execu/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Blue Team Explore Hunting For Indications of execu</h1>
</section>
<section class="spt-content">

# Blue Team Explore: Hunting For Indications of execute_assembly

Owner: Mike Sterrett

## Introduction

In this exercise, we’ll explore telemetry to detect:

- **Process Injection**
- **Inter-Process Communications (IPC)**
- **C2 (Command & Control) Communications**

We’ll use Mythic, Wazuh, and PowerPoint for diagramming our findings.

---

### **1. Initial Setup**

1. **Log into the assumed breach host** in the lab.
2. **Execute Initial Access Payload**:
    - Open the **Run** dialog.
    - The last command should be pre-filled; click **OK** to execute it.
    - Confirm you get a callback in Mythic.
3. **Set Tab Description**:
    - In Mythic, set the tab description to `Blue Team Explore`.
4. **Switch to Reverse Engineering Host**:
    - Register the **service_scan_demo.exe** assembly in Mythic using the modal dialog.
    - Task the agent to load it.

---

### **2. Configure Injection Technique**

1. In Mythic, run:
    - `get injection techniques`
    - Set the technique to `syscallx64 NtCreateThreadx`.
2. Set the spawn process to `smartscreen.exe` using the modal dialog.
3. Execute `service_scan_demo.exe` and wait for results.

---

### **3. Reviewing Execute Assembly Documentation**

1. In Mythic:
    - View **metadata** for `execute assembly`.
    - This confirms it:
        - Uses the .NET CLR loader.
        - Injects into a **sacrificial process** defined by `spawn_to_x64`.
        - Indicates **process injection** into `smartscreen.exe`.
2. **Conclusion**: The original PowerShell process will open a handle to `smartscreen.exe`.

---

### **4. Hunting in Wazuh for Process Injection**

1. Go to **Explore → Discover** in Wazuh.
2. Change the index pattern to `wazuh-archives`.
3. Search for **Sysmon Event ID 10** (process access events).
4. Use Boolean AND conditions:
    - **Source Image**: `powershell.exe`
    - **Target Image**: `smartscreen.exe`
5. Fix case sensitivity issues:
    - `eventdata` field is lowercase, but `sourceImage` is camel case.
6. **Key IOC Found**:
    
    PowerShell accessed SmartScreen with **Granted Access = 0x1ffff** (full process rights).
    

---

### **5. Diagramming IOCs**

- Use **PowerPoint** for IOC diagrams.
- Add:
    - PowerShell process details (image name, process GUID in red).
    - C2 communication icon.
    - Granted access details for process injection.

---

### **6. Identifying C2 Communications**

1. Search by **Process GUID** for the PowerShell process.
2. Identify network events:
    - **Event ID 3** (Network Connect).
    - Destination IP: `192.168.100.101` (attack infrastructure).
    - Destination Port: `80`.
3. Add to diagram:
    - Beaconing process with port details.

---

### **7. Investigating Target Process (smartscreen.exe)**

1. Search for events linked to **SmartScreen’s Process GUID**.
2. Identify:
    - **Pipe creation** (Event ID 17) → Named Pipe with a unique GUID.
    - **Pipe connection** (Event ID 18) → Unexpectedly connected by `System` process.
3. Add pipe creation & connection to diagram (mark pipe name in red).

---

### **8. Loaded Modules Analysis**

1. Search for **Image Load Events** for SmartScreen process.
2. Identify .NET DLLs:
    - `System.Management.ni.dll`
    - `System.Core.dll`
    - `System.Xml.dll`
    - Others.
3. **Hypothesis**: These may be loaded because our .NET assembly was injected.

---

### **9. Key Takeaways**

- **Process Injection IOC**:
    - PowerShell accessing SmartScreen with `0x1ffff` rights.
- **IPC IOC**:
    - Named pipe creation & connection events.
- **C2 IOC**:
    - Outbound connections to attacker IP on port 80.
- **Possible Anomaly**:
    - SmartScreen loading multiple .NET DLLs.

---

### **10. Next Steps**

- Before next lecture:
    - Complete your IOC diagram.
    - Map process injection, pipe events, and C2 communications.
- **Next Lab**:
    - Enable Sysmon Event IDs 12, 13, and 14 (registry events).
    - Hunt for malicious registry changes linked to Attack Path Level One.

</section>
</div>
