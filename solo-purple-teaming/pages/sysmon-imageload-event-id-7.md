---
layout: default
title: "Sysmon ImageLoad Event ID 7"
permalink: /solo-purple-teaming/sysmon-imageload-event-id-7/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Sysmon ImageLoad Event ID 7</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **1. Purpose of the Exploration Phase**

Before enabling **Sysmon Event ID 7**, remember the purpose of the *exploration phase* in the blue team activities:

- **Identify telemetry gaps** – Determine what’s missing from our collected logs.
- **Identify detection opportunities** – Find where the available telemetry can be leveraged to spot suspicious activity.

> In this phase, you’ll often perform independent research to understand what telemetry exists and how it can be used for detections.
> 
> 
> This course streamlines that process so you can focus on practical implementation.
> 

---

### **2. Researching Command Line Arguments in Telemetry**

- To find ways of capturing command line arguments in Windows, search for:
    
    ```
    capture command line arguments Windows Logging
    
    ```
    
- AI or search results may reveal multiple solutions:
    - **Sysmon** (our current choice)
    - **Group Policy** – enabling command line arguments for process creation events in the Windows Security log (Event ID 4688).

---

### **3. Understanding Unsigned Image Loads**

- **Why monitor unsigned image loads?**
    - Malware or custom payloads are often unsigned, making this a strong detection indicator.
- To check if a binary is signed:
    1. On your reverse engineering machine, use Sysinternals `sigcheck`.
    2. Example:
        
        ```
        sigcheck goodbyeamsi.exe
        
        ```
        
    3. If unsigned, it will show as **untrusted code**.

---

### **4. Researching How to Capture Unsigned Image Loads**

- Search for:
    
    ```
    capture unsigned image load events windows
    
    ```
    
- Sysmon **Event ID 7** logs image loads by processes and includes a **Signed** field:
    - `True` → Binary is signed
    - `False` → Binary is unsigned

---

### **5. Checking for Existing Event ID 7 Telemetry**

- In **Wazuh**:
    1. Navigate to **Explore → Discover**.
    2. Filter for `Event ID: 7`.
    3. If no results in the last 12 hours → proceed to enable it in Sysmon.

---

### **6. Updating Sysmon Configuration**

1. On the **Assume Breach Host**:
    - Open the Sysmon configuration.
    - Locate **Event ID 7** rules.
    - Change the `<Include>` rule to capture **all events where `Signed = false`**.
2. Save the configuration.
3. In **PowerShell (Admin)**:
    
    ```powershell
    cd Downloads
    sysmon -c sysmonconfig.xml
    
    ```
    
    - Ensure configuration is **validated and updated**.

---

### **7. Verifying in Event Viewer**

- Open **Event Viewer** → **Applications and Services Logs** → **Microsoft → Windows → Sysmon → Operational**.
- Filter for `Event ID: 7`.
- Confirm unsigned image load events appear.

---

### **8. Verifying in Wazuh**

- Switch **Index Pattern** to:
    
    ```
    int_wazuh-archives-*
    
    ```
    
- Search for `Event ID: 7`.
- Confirm new entries are populating in the last few minutes.

---

### **9. Testing with the Initial Access Payload**

1. On the **Assume Breach Host**:
    - Rerun `goodbyeamsi.exe`.
2. In **Mythic**:
    - Verify a new callback is established.
3. In **Wazuh**:
    - Search for `Event ID: 7` alerts related to `goodbyeamsi.exe`.
    - Switch to **wazuh-archives-**index to see all raw events.

---

### **10. Filtering for the Payload’s Image Loads**

1. Filter by:
    
    ```
    data.win.eventdata.ImageLoaded : *amsi*
    
    ```
    
    - This shows all image loads related to `goodbyeamsi.exe` and its DLLs.
2. Expand event details.
3. Check:
    
    ```
    data.win.eventdata.Signed : False
    
    ```
    
    - Confirms the binary is **unsigned**.

---

### **11. Completion Check**

✅ Before moving on:

- You can see unsigned image load events in **Wazuh Manager**.
- You have verified the detection works with your initial access payload.

---

**Next Step:** In the following lecture, you will enable **Sysmon Event ID 11** (File Create events) to expand telemetry coverage.

</section>
</div>
