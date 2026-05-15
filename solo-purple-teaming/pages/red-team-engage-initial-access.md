---
layout: default
title: "Red Team Engage - Initial Access"
permalink: /solo-purple-teaming/red-team-engage-initial-access/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Red Team Engage - Initial Access</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **1. Review of Level 1 Attack Path**

In Level 1, our attack sequence will follow these stages:

1. **Initial Access**
    - Use the **Click-Fix technique** (TTP) via the Windows Run dialogue.
    - Execute a PowerShell command that **reflectively loads** our Stager (Apollo agent) into memory.
2. **Enumeration**
    - Run our **Service Scan utility** to bypass service enumeration detections.
    - The Service Scan is implemented as a custom .NET assembly.
    - Register the assembly and use **execute-assembly** to run it in memory.
3. **Privilege Escalation**
    - Use enumeration results to identify a vulnerable service binary.
    - Overwrite the binary and escalate privileges.
4. **Lateral Movement**
    - Move to the Domain Controller (DC).
    - Establish a foothold on the DC.

---

### **2. Setting Up the New Operation**

1. **Close Out the Previous Operation**
    - In Mythic, open the **ecoin attack path level 0** operation.
    - Click **Configure/Edit** → toggle **Operation to Complete** → click **Update**.
    - Refresh the page and confirm the operation is marked green (completed).
2. **Create a New Operation**
    - Name it: `ecoin attack path level 1`.
    - Click **Submit**.
    - Set it as the **current operation**.
    - Verify the top banner shows `ecoin attack path level 1`.

---

### **3. Generating a Payload**

1. In Mythic, click **Payloads** → **Actions** → **Generate New Payload**.
2. Select **Windows** as the target platform.
3. Keep all defaults and click **Next**.
4. **Commands**: Either move all over now or register as needed later.
5. **Transport**: Select **HTTP** and set the callback address:
    
    ```
    http://192.168.100.101
    
    ```
    
6. Name the binary: `Apollo.exe`.
7. Click **Create Payload**.
8. Download the generated payload.

---

### **4. Transferring the Payload to the Attack Host**

If you generated the payload **on another machine**:

1. Open a terminal and navigate to the payload’s folder (e.g., `Downloads`).
2. Use `scp` to transfer to your Kali attacker host:
    
    ```bash
    scp Apollo.exe jigglow@192.168.100.101:~/Downloads
    
    ```
    
3. On your local machine, delete the payload for OPSEC.

If you generated it **directly on Kali**, this transfer step is unnecessary.

---

### **5. Preparing the Payload for Staging**

1. SSH into your Kali attacker host:
    
    ```bash
    ssh jigglow@192.168.100.101
    
    ```
    
2. Navigate to the payload location:
    
    ```bash
    cd ~/Downloads
    ls -al
    
    ```
    
    - Confirm `Apollo.exe` was modified today.
3. Start an HTTP server to stage the payload:
    
    ```bash
    python3 -m http.server 80
    
    ```
    

---

### **6. Executing the Initial Access**

1. On the target (soon-to-be breached host), open the Windows **Run** dialogue (`Win + R`).
2. Your last-tested PowerShell command should already be present (stored in a registry key).
3. Click **OK** to execute the command.
4. In Mythic, go to **Callbacks**.
    - Verify you have a **new callback** indicating successful initial access.

---

### **7. Verifying Undetected Execution**

1. In **Wazuh**, open the **Solo Purple Teaming Dashboard**.
2. Refresh and check for detections:
    - **No events** should be present.
    - This confirms our initial access was **undetected**.

---

### **8. Next Steps**

In the following phase, you will:

- Perform **enumeration** using Service Scan via **execute-assembly**.
- Identify a vulnerable service binary.
- Overwrite the binary to escalate privileges.
- Obtain a high-integrity callback.

---

✅ **End of Level 1 Engage Phase – Initial Access Setup**

</section>
</div>
