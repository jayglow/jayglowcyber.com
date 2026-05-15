---
layout: default
title: "Blue Team Explore - Enabling Sysmon Event IDs 10,"
permalink: /solo-purple-teaming/blue-team-explore-enabling-sysmon-event-ids-10/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Blue Team Explore - Enabling Sysmon Event IDs 10,</h1>
</section>
<section class="spt-content">

# Blue Team Explore - Enabling Sysmon Event IDs 10, 17 & 18

Owner: Mike Sterrett

## **Objective**

In this lab, you will enable Sysmon logging for:

- **Event ID 10** – Process Access
- **Event ID 17** – Pipe Created
- **Event ID 18** – Pipe Connected

You will configure Sysmon to capture all process access events with full access rights, then verify event ingestion in Wazuh.

---

### **1. Prepare the Sysmon Configuration**

1. Log into the **Soon Breach** host.
2. Navigate to the **base Sysmon configuration** file located in your **Downloads** folder.
3. Locate the **Event ID 10** rule group.
4. Add an XML entry to match **Granted Access** with the value `0x1fffff` (full process access).
    - Use the `<grantedAccess>` tag.
    - Set the **condition** to `"is"`.
    - Set the **match value** to `0x1fffff`.

---

### **2. Apply the Updated Sysmon Config**

1. Open **Command Prompt as Administrator**.
2. Change directory to:
    
    ```bash
    cd C:\Users\rlin\Downloads
    
    ```
    
3. Update Sysmon’s configuration:
    
    ```bash
    sysmon64 -c baseconfig.xml
    
    ```
    
4. Confirm that the configuration file is validated and updated.

---

### **3. Verify Event ID 10 in Wazuh**

1. Open the **Wazuh console**.
2. Go to **Discover** under **Explore**.
3. Switch your **index pattern** to **archives**.
4. Search for:
    
    ```
    data.win.system.eventID: 10
    
    ```
    
5. Confirm process access events are appearing from the `ecoin-wrk-rlin` host.
6. Expand an event and verify that the **Granted Access** field shows:
    
    ```
    0x1fffff
    
    ```
    

---

### **4. Resolve Field Mapping Warnings**

1. In Wazuh, navigate to **Dashboard Management** → **Index Patterns**.
2. Select **Wazuh archives**.
3. Click **Refresh field list**.
4. Return to **Discover**, reapply your Event ID 10 filter, and confirm there are no warnings.

---

### **5. Enable Event IDs 17 and 18**

1. Return to your Sysmon config file on the **Soon Breach** host.
2. Modify the relevant section by changing **include** to **exclude** as required for your rule structure.
3. Save the file.
4. In **Command Prompt as Administrator**, reapply the config:
    
    ```bash
    sysmon64 -c baseconfig.xml
    
    ```
    

---

### **6. Verify Events 17 and 18**

1. In Wazuh Discover, filter for:
    
    ```
    data.win.system.eventID: 17
    
    ```
    
    Confirm **Pipe Created** events appear.
    
2. Then filter for:
    
    ```
    data.win.system.eventID: 18
    
    ```
    
    Confirm **Pipe Connected** events appear.
    

---

### **7. Final Verification**

Ensure all three event types are present in Wazuh:

- **Event ID 10** – Process Access (Granted Access = `0x1fffff`)
- **Event ID 17** – Pipe Created
- **Event ID 18** – Pipe Connected

---

✅ **Before moving to the next lecture:**

Verify you can see **Sysmon process access**, **pipe connect**, and **pipe create** events in Wazuh.

</section>
</div>
