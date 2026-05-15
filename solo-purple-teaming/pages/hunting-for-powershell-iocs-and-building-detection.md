---
layout: default
title: "Hunting For PowerShell IOCs and Building Detection"
permalink: /solo-purple-teaming/hunting-for-powershell-iocs-and-building-detection/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Hunting For PowerShell IOCs and Building Detection</h1>
</section>
<section class="spt-content">

# Hunting For PowerShell IOCs and Building Detections

Owner: Mike Sterrett

## **1. Understanding the Goal**

In this lab, we will:

- Hunt for evidence of malicious PowerShell activity from **Attack Path Level Zero**.
- Learn how **PowerShell Script Block Logging** works and why it’s essential for defenders.
- Use Wazuh to:
    1. Search for suspicious PowerShell activity.
    2. Identify suspicious script content.
    3. Create a **custom Wazuh rule** to detect it.
    4. Test the detection by re-running our simulated attack.

---

### **2. Why Script Block Logging is Important**

- **Script Block Logging** captures **the entire contents** of executed PowerShell scripts — even dynamically generated code.
- Unlike basic logging, it detects:
    - Encoded or hidden execution.
    - Remote download-and-execute commands.
    - Attempts to disable security controls.
- For **detection engineering**, this gives you behavior-based detection capability instead of relying only on:
    - Command-line flags.
    - Process names.
- In **solo purple teaming**, you can:
    - Simulate malicious PowerShell activity.
    - Verify whether your rules catch it.
    - Refine detections based on real telemetry.

---

### **3. Hunting for Malicious PowerShell in Wazuh**

**Steps:**

1. **Log in to Wazuh Manager.**
2. Navigate to **Explore → Discover** (this gives access to all events).
3. Change the **index pattern** to:
    
    ```
    wazuh-archives-*
    
    ```
    
    (This ensures you’re searching all stored events.)
    
4. **Filter for PowerShell Script Block events** by Event ID:
    
    ```
    data.win.system.eventID:4104
    
    ```
    
    - 4104 = PowerShell Script Block Logging event.
    - Example result: **38 hits** in our lab environment.
    - In production: expect **thousands to millions** of results.
5. Use **keyword searching** to find suspicious commands.
    
    Example:
    
    ```
    invoke
    
    ```
    
    - Found multiple **Invoke-WebRequest** instances.
    - Expand a result → Check `scriptblocktext` field.
    - The script in our lab:
        - Downloads `Apollo.exe` using `Invoke-WebRequest`.
        - Loads it reflectively using `.NET Reflection`.

**Why it’s suspicious:**

- `Invoke-WebRequest` + reflective loading are common malicious behaviors.

---

### **4. Creating a Custom Wazuh Rule**

**4.1. Find the correct SID for Script Block Logs**

- Go to **Server Management → Rules**.
- Search for “PowerShell”.
- Identify:
    - `91801` = Group of Windows PowerShell operational channel rules.
    - `91802` = Group for **PowerShell Script Block Logging** rules.
        - Confirmed by comment: *"PowerShell script block rules"*.
- We will use **SID 91802**.

**4.2. Edit the Solo Purple Teaming rules file**

- Search for:
    
    ```
    solo_purple_teaming.xml
    
    ```
    
- Add a new rule block:
    
    ```xml
    <group name="solo_purple_teaming,windows,powershell">
        <rule id="100102" level="15">
            <if_sid>91802</if_sid>
            <match>invoke web request</match>
            <description>Detected malicious PowerShell activity: Invoke-WebRequest</description>
        </rule>
    </group>
    
    ```
    
    **Notes:**
    
    - Group name: includes `solo_purple_teaming`, `windows`, and `powershell`.
    - Rule ID: `100102` (next in sequence).
    - Level: `15` = critical/high fidelity.
    - `if_sid`: ensures we only match Script Block events.
    - `match`: simple regex for `invoke web request`.

**4.3. Save and restart Wazuh Manager**

---

### **5. Testing the Rule**

**Option 1 – Using `rule-test`**

- Run:
    
    ```bash
    cat archives.json | grep "invoke web request" | tail -n 1 > test_event.json
    /var/ossec/bin/wazuh-logtest < test_event.json
    
    ```
    
- *Note:* `rule-test` may not always behave exactly like production detection.

**Option 2 – Live Attack Simulation**

- Re-run the **initial access payload**.
- In Wazuh, check:
    - **Solo Purple Teaming dashboard**.
    - **Time filter**: last 5 minutes.
    - Look for **critical event** with:
        
        ```
        Detected malicious PowerShell activity: Invoke-WebRequest
        
        ```
        

---

### **6. Troubleshooting Notes**

- Sometimes correlation events may not trigger immediately — ensure:
    - Rules are active.
    - Script Block Logging is enabled.
    - Manager is fully restarted.
- In production: you must **thoroughly test** before deployment.

---

### **7. Next Challenge**

- Build detections for:
    - **Service enumeration** activities from Attack Path Level Zero.
    - PowerShell **reflection-based loading**.

---

**Key Takeaways:**

- **Script Block Logging** is vital for detecting malicious PowerShell.
- Hunting is about **behavior** recognition, not just known signatures.
- In Wazuh:
    - Use SIDs to target the right log type.
    - Use regex matching to pinpoint suspicious commands.
- Solo purple teaming accelerates detection tuning by leveraging known attack paths.

</section>
</div>
