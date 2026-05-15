---
layout: default
title: "Configuring Wazuh for Defender and Sysmon"
permalink: /solo-purple-teaming/configuring-wazuh-for-defender-and-sysmon/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Configuring Wazuh for Defender and Sysmon</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **1. Recap of Lab Environment**

- **AllSafe LAN**: Wazuh server resides here.
- **Assumed Breach Host**:
    - Wazuh agent installed and forwarding events to AllSafe via port forwarding on the AllSafe edge device.
    - Sysmon installed and verified to be logging events.

![image.png](Configuring%20Wazuh%20for%20Defender%20and%20Sysmon/image.png)

Goal: Configure Wazuh agent to forward **Sysmon** and **Windows Defender** logs to the Wazuh server.

---

### **2. Key Concepts Before Configuration**

### **a. Agent Groups**

- Logical collections of agents for easier management.
- Group assets by:
    - **Role** (e.g., workstations, domain controllers)
    - **Location**
    - **Threat exposure**
- **Benefits**:
    - Apply settings, rules, and policies to an entire group at once.
    - Isolate policies for different asset types.
    - Improve scalability and manageability.

### **b. Configuration Methods**

- **Agent-based**: Configurations live on the endpoint (max flexibility, harder to manage at scale).
- **Centralized**: Configurations pushed from Wazuh Manager to agents (consistent, scalable, lower error risk).
- **Recommendation**: Small/varied environments → Agent-based; Large/standardized → Centralized.

### **c. Configuration Precedence**

- **Local**: `ossec.conf` (on the agent).
- **Central**: `agent.conf` (on the manager) — **overrides** local settings.

### **d. `logall` Setting**

- **Default**: Disabled — only sends events matching detection rules.
- **`log_all` or `log_all_JSON`**: Sends all collected logs to the manager.
    - Required for creating searchable indices in Wazuh Dashboard.
    - Use `log_all_JSON` for full JSON-structured archives (preferred for indexing).

---

### **3. Lab Configuration Steps**

### **Step 1: Create a Windows Agent Group**

1. Log into **Wazuh Console** → Hamburger menu → **Agent Management** → **Groups**.
2. Click **Add New Group** → Name: `Windows` → Save.
3. Move the assumed breach host to this group:
    - Dashboard → Overview → **Active Agents**.
    - Click the three dots (…) next to the agent → **Edit Groups**.
    - Remove from default, add to **Windows** → Save.

---

### **Step 2: Enable Sysmon & Windows Defender Log Collection**

1. **Edit the Windows Group Configuration**:
    - Hamburger menu → **Agent Management** → **Groups**.
    - Click the pencil next to `Windows`.
2. Add these entries:

```xml
<localfile>
  <log_format>eventchannel</log_format>
  <location>Microsoft-Windows-Windows Defender/Operational</location>
</localfile>

<localfile>
  <log_format>eventchannel</log_format>
  <location>Microsoft-Windows-Sysmon/Operational</location>
</localfile>

```

1. Save — confirm “File successfully edited” message.

---

### **Step 3: Enable Full Log Collection on Wazuh Manager**

1. SSH into **Wazuh Manager**:
    
    ```bash
    cd /var/ossec/etc
    sudo vi ossec.conf
    
    ```
    
2. Locate:
    
    ```xml
    <logall_json>no</logall_json>
    
    ```
    
3. Change to:
    
    ```xml
    <logall_json>yes</logall_json>
    
    ```
    
4. Save & exit → Restart Wazuh Manager:
    
    ```bash
    sudo systemctl restart wazuh-manager
    
    ```
    

---

### **Step 4: Enable Archives in Filebeat**

1. Navigate:
    
    ```bash
    cd /etc/filebeat
    sudo vi filebeat.yml
    
    ```
    
2. Locate:
    
    ```yaml
    archives:
      enabled: false
    
    ```
    
3. Change to:
    
    ```yaml
    archives:
      enabled: true
    
    ```
    
4. Restart Filebeat:
    
    ```bash
    sudo systemctl restart filebeat
    sudo systemctl status filebeat
    
    ```
    

---

### **Step 5: Create an Archives Index Pattern in Wazuh Dashboard**

1. Wazuh Dashboard → **Dashboard Management** → **Index Patterns**.
2. Click **Create Index Pattern** → Name:
    
    ```
    winwazuh-archives-*
    
    ```
    
3. Select **`timestamp`** as the time field → **Create Index Pattern**.

---

### **Step 6: Verify Event Ingestion**

1. Go to Hamburger menu → **Explore** → **Discover**.
2. Select `wazuh-archives-*` index pattern.
3. Confirm **Sysmon** and **Windows Defender** events are visible.

---

### **Step 7: Trigger a Windows Defender Alert (Test)**

1. SSH to **Kali server**.
2. Copy Mimikatz to the web-served downloads directory:
    
    ```bash
    sudo cp /opt/mythic/mythic/installed_services/Apollo/agent_code/mimikatz/x64/mimikatz.exe ~/downloads/
    
    ```
    
3. On the **Assumed Breach Host**, open browser → Download Mimikatz from:
    
    ```
    http://<Kali_IP>:8000/mimikatz.exe
    
    ```
    
4. Windows Defender should trigger an alert.
5. Check Wazuh Dashboard → **Overview**:
    - Confirm **high-severity alert** from Windows Defender.
    - Verify alert details and rule match.

---

### **8. Checklist Before Moving On**

✅ Windows & Sysmon logs enabled in `agent.conf`.

✅ `log_all_JSON` set to **yes** in `ossec.conf`.

✅ Archives enabled in **Filebeat**.

✅ Archives index created in Wazuh Dashboard.

✅ Verified Defender & Sysmon logs are ingesting.

---

### **Next Lecture**

We will configure **Wazuh to consume Syslog events** and set up **Ecoin Edge** to forward those events to Wazuh.

</section>
</div>
