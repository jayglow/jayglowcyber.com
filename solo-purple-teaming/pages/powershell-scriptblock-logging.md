---
layout: default
title: "PowerShell ScriptBlock Logging"
permalink: /solo-purple-teaming/powershell-scriptblock-logging/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>PowerShell ScriptBlock Logging</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **Overview**

In this lab, we will enable **PowerShell Script Block Logging** on the assumed breached host and verify that the telemetry from our initial access payload is captured both locally and in the **Wazuh Manager**. Script Block Logging records the *entire* content of executed PowerShell code, including obfuscated or encoded scripts, making it an essential capability for **solo purple teaming**.

---

### **Why Script Block Logging Matters**

- Captures **actual script logic**, not just command lines.
- Detects malicious PowerShell activity such as **LOLBins**, encoded payloads, and reflective loading.
- Critical for **red-to-blue feedback loops**—every simulated malicious action can be validated from the defender’s perspective.
- Supports high-fidelity detection tuning and hardening against bypass techniques.

---

## **Step-by-Step Lab Instructions**

### **1. Connect to the Assumed Breach Host**

1. Use **Remote Desktop** to log in to the assumed breach host.
2. Open a **PowerShell window** as **Administrator**.

---

### **2. Open Group Policy Management**

1. In the PowerShell window, type `mmc` and press **Enter** to open the Microsoft Management Console.
2. Go to **File → Add/Remove Snap-in**.
3. Select **Group Policy Management** and click **Add** → **OK**.

---

### **3. Edit the Default Domain Policy**

1. In **Group Policy Management**, navigate to the **Default Domain Policy**.
2. Right-click **Edit**.

---

### **4. Navigate to PowerShell Logging Settings**

1. In the Group Policy Editor, go to:
    
    ```
    Computer Configuration
       → Policies
          → Administrative Templates
             → Windows Components
                → Windows PowerShell
    
    ```
    
2. Here you will see several valuable settings:
    - Module Logging
    - Script Block Logging
    - Script Execution
    - PowerShell Transcription

---

### **5. Enable Script Block Logging**

1. Double-click **Turn on PowerShell Script Block Logging**.
2. Select **Enabled**.
3. Check the box to log **invocation start/stop events** (generates Event IDs **4103**, **4104**, and others).
4. Click **Apply** → **OK**.
5. Confirm the **State** is now **Enabled** in Group Policy Management.
6. Close the editor.

---

### **6. Apply the Policy**

1. Back in the **PowerShell (Admin)** window, run:
    
    ```powershell
    gpupdate /force
    
    ```
    
2. Wait for both **Computer Policy** and **User Policy** updates to complete.

---

### **7. Test with Initial Access Payload**

1. Navigate to **Downloads**.
2. Run your **initial access payload** to establish a new callback.
3. Verify in your C2 framework (e.g., Mythic) that the callback is active.

---

### **8. View Script Block Logs Locally**

1. In PowerShell, type:
    
    ```powershell
    eventvwr
    
    ```
    
    and press **Enter**.
    
2. Navigate to:
    
    ```
    Applications and Services Logs
       → Microsoft
          → Windows
             → PowerShell
                → Operational
    
    ```
    
3. Look for **Event ID 4104**.
4. Inspect the log to see:
    - **Invoke-WebRequest** fetching the Apollo agent.
    - **System.Reflection.Assembly.Load** for reflective loading.
    - Payload execution details.

---

### **9. Configure Wazuh to Capture Script Block Logs**

1. In the **Wazuh Manager**, go to:
    - **Agent Management** → **Groups**.
    - Select the **Windows group** configured for Sysmon logs.
2. Edit the group configuration:
    - Copy an existing `<localfile>` tag.
    - Paste below the existing entry.
    - Change the channel to:
        
        ```xml
        <localfile>
          <location>Microsoft-Windows-PowerShell/Operational</location>
          <log_format>eventchannel</log_format>
        </localfile>
        
        ```
        
3. Save the configuration.

---

### **10. Push Config to Agents**

- The **auto-restart** setting on the agent will apply the new config automatically.

---

### **11. Verify in Wazuh**

1. In Wazuh, go to:
    - **Discover → Archives**.
2. Search for:
    
    ```
    4104
    
    ```
    
3. If no results appear:
    - Return to the assumed breach host.
    - Rerun the initial access payload.
    - Wait for a new callback in Mythic.
4. Refresh the Wazuh search:
    - You should now see **Event ID 4104** entries.
    - The **script_block_text** field should contain the entire PowerShell code from the payload.

---

### **Final Check**

- Confirm that:
    - Script Block Logging is enabled in Group Policy.
    - Logs appear both locally (**Event Viewer**) and in **Wazuh**.
    - The captured script clearly shows the malicious PowerShell activity.

---

✅ **You have successfully enabled PowerShell Script Block Logging, verified telemetry capture locally, and integrated it into your centralized logging platform for detection and analysis.**

</section>
</div>
