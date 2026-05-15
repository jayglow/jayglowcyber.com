---
layout: default
title: "Creating Custom Rule for PowerShell Reflection"
permalink: /solo-purple-teaming/creating-custom-rule-for-powershell-reflection/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Creating Custom Rule for PowerShell Reflection</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **Objective**

In this lab, you’ll create a **custom Wazuh rule** to detect **PowerShell reflection activity** using Script Block Logging data. This is part of building high-fidelity detections for our initial access payloads.

---

### **1. Rule Overview**

Our rule will:

- Have a **Rule ID**: `100103`
- **Level**: `15` (high severity)
- Match against the **if_sid** `91802` (PowerShell Script Block Logging event).
- Search for the keyword **"reflection"** in the event data.
- Include a descriptive message for easy identification.

---

### **2. Navigating to the Custom Rules File**

1. **Log into the Wazuh Manager**.
2. Go to **Server Management**.
3. Select **Rules**.
4. Locate and open your **custom rules file** — in this case:
    
    `solo_purple_teaming_rules.xml`
    
5. Find the **PowerShell grouping** section where you want to add the new rule.

---

### **3. Creating the Rule**

Add a new `<rule>` block inside the PowerShell group:

```xml
<rule id="100103" level="15">
    <if_sid>91802</if_sid>
    <match>reflection</match>
    <description>Detected PowerShell reflection</description>
</rule>

```

**Key points:**

- `id` must be unique and within your chosen custom rules range.
- `level` indicates the severity (15 is very high).
- `if_sid` refers to the base event ID for Script Block Logging.
- `match` searches the event data for our keyword.
- `description` is what you’ll see in alerts.

---

### **4. Saving and Restarting Wazuh**

- Save the changes to your rules file.
- Restart the Wazuh Manager to apply changes:
    
    ```bash
    sudo systemctl restart wazuh-manager
    
    ```
    

---

### **5. Testing the Rule**

1. **Run the Initial Access Payload**
    - Double-click on **Goodbye MZ** to execute the payload.
2. **Search for the Alert in Wazuh**
    - Go to **Discover** in Wazuh.
    - Search by Rule ID:
        
        ```
        rule.id:100103
        
        ```
        
    - Verify that the alert appears for the reflection event.
3. **Inspect the Alert**
    - Expand the event details.
    - Confirm that the **description** says: *Detected PowerShell reflection*.

---

### **6. Next Steps**

- Ensure you have **at least two custom PowerShell rules** before proceeding:
    1. One for `Invoke-WebRequest` (from the previous lecture).
    2. One for **reflection** (this lecture).
- In the next session, you’ll build a **Solo Purple Teaming Dashboard** from scratch to visualize these detections.

</section>
</div>
