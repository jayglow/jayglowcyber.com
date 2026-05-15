---
layout: default
title: "Privilege Escalation"
permalink: /solo-purple-teaming/privilege-escalation/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Privilege Escalation</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **1. Understanding Privilege Escalation**

- **Definition**: Gaining higher permissions than initially granted (e.g., from standard user to Administrator or SYSTEM).
- **Scenario**: We start with **medium integrity level** as `rlynn` (Rachel Lynn) and aim to escalate.
- **Purpose for Solo Purple Teaming**:
    - Test detection of privilege escalation.
    - Bridge from initial access to more impactful actions.
    - Enable lateral movement and persistence.

---

### **2. Types of Privilege Escalation**

- **Vertical Escalation**:
    - From standard user → Administrator.
    - Primary goal: unlock broader control.
- **Horizontal Escalation**:
    - Same privilege level, different accounts.
    - Useful for evasion or alternate data access.

---

### **3. Common Privilege Escalation Techniques**

We focus on **misconfigured services** for this lab, but others include:

- **Unquoted service paths**
- **DLL hijacking/sideloading**
- **Insecure token handling** (e.g., `SeImpersonatePrivilege`)
- **UAC bypass**
- **Credential harvesting** (`LSASS`, SAM, DPAPI secrets)

---

### **4. Lab Goal**

We’ll:

1. Install the **Mythic service_wrapper agent**.
2. Attempt privilege escalation via a **vulnerable service**.
3. Discover Defender detection.
4. Write our **own custom service wrapper** to evade detection.

---

### **5. Installing Service Wrapper in Mythic**

1. SSH into Kali host and run these commands:
    
    ```bash
    cd /opt/mythic/mythic
    sudo ./mythic_cli install github https://github.com/MythicAgents/service_wrapper
    
    ```
    
2. Update if prompted.
3. Disconnect from internet to avoid payload signature submissions.

---

### **6. Generating the Payload**

1. **In Mythic UI** → Payloads → Generate new payload.
2. Agent: **Apollo**, Type: **Shellcode** (binary format).
3. Commands: Move all over → Next.
4. C2 Profile: **HTTP**.
5. Callback Host: **WAN IP** (e.g., `192.168.100.101`).
6. Output name: `apollo.bin`.

---

### **7. Creating the Service Wrapper Payload**

1. Payload Type: **Windows**, Framework: `.NET 4.0`, Arch: `x64`.
2. Select the shellcode payload.
3. Name: `apollo_service_wrapped.exe`.
4. Download and stage the payload on the Kali host.

---

### **8. Detection by Defender**

- Attempting to run `apollo_service_wrapped.exe` triggers **Windows Defender detection**.
- **Reason**: Service wrapper uses suspicious API calls (`VirtualProtect`, `VirtualUnlock`) linked to shellcode injection.
- **Next Step**: Build a custom wrapper to avoid detection.

---

### **9. Writing a Custom Service Wrapper**

**On Reverse Engineering VM:**

1. Open Visual Studio → New Project → Windows Service (.NET Framework).
2. Name: `ecoin_sync`.
3. **In `Service1.cs`**:
    - Add a background thread in `OnStart`.
    - Create `DoWork()` method to:
        - Launch `powershell.exe`.
        - Run `Apollo.exe` from Downloads.
        - Hide window, redirect output.
        - Keep service alive with loop.
4. **OnStop()**:
    - Abort the worker thread if running.
5. Build solution:
    - Platform: `x64`, Configuration: `Release`.
6. Stage to Downloads folder on Kali:
    
    ```bash
    scp ecoin_sync.exe user@staging_server:/downloads
    
    ```
    

---

### **10. Deploying the Custom Wrapper**

1. In Mythic, confirm **PowerShell command** is available.
2. Use PowerShell `Invoke-WebRequest` to download `Apollo.exe` and `ecoin_sync.exe` to the target’s `Downloads` or `Desktop`.
3. Rename existing service binary to preserve it.
4. Replace with malicious `ecoin_sync.exe`.

---

### **11. Starting the Service**

1. Query service status:
    
    ```bash
    shell sc query ecoin_sync
    
    ```
    
2. Start service:
    
    ```bash
    shell sc start ecoin_sync
    
    ```
    
3. **Result**: New Mythic callback appears as `SVC_ecoin_sync` with **high integrity** privileges.

---

### **12. Verification**

- Check **metadata** in Mythic for `Elevation Level = High`.
- We now have **Administrator-level access**.

---

### **Key Takeaways**

- Privilege escalation is critical for expanding attacker capabilities.
- Defender detections often focus on injection-related API calls.
- Custom tools can bypass default detections.
- Always test both offensive execution and defensive telemetry.

---

**Next Lecture Preview**: We’ll set up telemetry collection with Wazuh and begin building detections for privilege escalation activity.

</section>
</div>
