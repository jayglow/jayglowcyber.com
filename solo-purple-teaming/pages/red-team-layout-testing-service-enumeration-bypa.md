---
layout: default
title: "Red Team Layout - Testing Service Enumeration Bypa"
permalink: /solo-purple-teaming/red-team-layout-testing-service-enumeration-bypa/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Layout - Testing Service Enumeration Bypa</h1>
</section>
<section class="spt-content">

# Red Team Layout - Testing Service Enumeration Bypass

Owner: Mike Sterrett

## **1. Objective**

In this lab, you’ll learn how to:

- Register and execute a .NET assembly (our custom `service_scan_demo.exe`) in Apollo.
- Understand how attackers stage tools in memory.
- Experiment with injection techniques to evade Windows Defender.
- Successfully run the service scan utility without detection.

---

### **2. Key Concepts**

### **Register Assembly**

- **Purpose:** Loads a .NET assembly into the agent’s memory for later execution.
- **Important:** *Does not execute the binary*—it stages it.
- **Why it matters:** Mirrors attacker behavior where tools are loaded once and executed when needed, minimizing footprint.

### **Execute Assembly**

- **Purpose:** Executes a previously registered .NET assembly in memory.
- **Process:** Spawns a process (default: `rundll32.exe`), injects the assembly, and runs it filelessly.
- **Footprint:** Still leaves process-related telemetry (child processes, command lines, suspicious binaries).

---

### **3. Dynamic Injection Techniques in Apollo**

Apollo supports three injection methods:

1. **CreateRemoteThread** – Classic and reliable, but noisy.
2. **Early Bird QueueUserAPC** – Stealthier; injects into a suspended process before its main thread starts.
3. **Syscalls NTCreateThreadEx** – Lower-level API; can evade EDRs focused on higher-level calls.

**Why experiment?**

Switching methods helps determine which are caught and which slip past defenses. This builds both offensive and defensive awareness.

---

### **4. Lab Steps**

### **Step 1 – Initial Access via Run Box**

1. Open **Run** (`Win + R`).
2. Paste and execute your PowerShell loader command.
3. Ensure your **staging server** is running so Apollo can be fetched and executed.
4. Verify the callback appears in Mythic.

---

### **Step 2 – Register the Service Scan Assembly**

1. In Mythic, select your callback.
2. Run:
    
    ```
    register_assembly
    
    ```
    
    This opens a file selector.
    
3. Browse to and select `service_scan_demo.exe`.
4. Task the agent to load it into memory.

---

### **Step 3 – Execute the Assembly**

1. Check usage:
    
    ```
    help execute_assembly
    
    ```
    
2. Run:
    
    ```
    execute_assembly service_scan_demo.exe
    
    ```
    
3. **Problem:** No results returned—likely due to **Windows Defender detection**.

---

### **Step 4 – Confirm Defender Detection**

1. Re-run `execute_assembly` and monitor for Defender alerts.
2. Verify Defender is catching and blocking the execution.

---

### **Step 5 – Test Alternate Injection Techniques**

1. Get available methods:
    
    ```
    get_injection_techniques
    
    ```
    
2. Try:
    
    ```
    set_injection_technique early_bird_queuuserapc
    execute_assembly service_scan_demo.exe
    
    ```
    
3. If still detected, switch to:
    
    ```
    set_injection_technique syscall_x64.ntcreatethreadex
    
    ```
    
4. Run `execute_assembly` again.

**Result:** Still detected—so injection method alone is insufficient.

---

### **Step 6 – Change Spawn-To Process**

1. Default spawn target: `rundll32.exe` (*commonly detected by Defender*).
2. Change it:
    
    ```
    spawn_to_x64
    
    ```
    
    Use the file picker to choose:
    
    ```
    C:\Windows\System32\smartscreen.exe
    
    ```
    
3. Task the agent.

---

### **Step 7 – Execute with New Spawn-To**

1. Run:
    
    ```
    execute_assembly service_scan_demo.exe
    
    ```
    
2. **Expected Outcome:**
    - No Defender alert.
    - Results returned (e.g., `EcoinSync` service modifiable by `rlin`).

---

### **Step 8 – Validate in Wazuh**

1. Refresh your Wazuh dashboard.
2. Confirm no detections triggered during execution.

---

### **5. Lessons Learned**

- **Staging vs Execution:** Always register first, then execute.
- **Defender Evasion:** Injection technique changes alone may fail—changing the spawn target can help.
- **Detection Planning:**
    - Execute Assembly uses named pipes and process injection.
    - Defenders should monitor for:
        - `Pipe Create` and `Pipe Connect` events.
        - `Process Access` events to the spawn target.
        - Unusual process-child relationships.

---

### **6. Next Steps**

In the next lab, you will:

- Add the `SVC_EcoinSync` account to the **Domain Admins** group (for lab purposes).
- Perform lateral movement to the Domain Controller.
- Execute the **full Attack Path Level 1** from start to finish.

</section>
</div>
