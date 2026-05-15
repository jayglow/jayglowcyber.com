---
layout: default
title: "Sysmon ProcessCreate Event ID 1 & NetworkConnect E"
permalink: /solo-purple-teaming/sysmon-processcreate-event-id-1-and-networkconnect-e/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Sysmon ProcessCreate Event ID 1 & NetworkConnect E</h1>
</section>
<section class="spt-content">

# Sysmon ProcessCreate Event ID 1 & NetworkConnect Event ID 3

Owner: Mike Sterrett

### **1. Understanding the Focus Events**

**Event ID 1 – Process Creation**

- Logs extended info about new processes.
- Includes **full command line**, process GUID, file hash, and hash type.
- Useful for spotting commands run during **host enumeration**.

**Event ID 3 – Network Connection**

- Logs **TCP and UDP** connections.
- Disabled by default in Sysmon.
- Includes **source/destination IP, ports, hostnames**, and links to the process via process GUID and PID.

---

## **2. Exploring Telemetry in Wazuh**

1. **Login to Wazuh Manager Dashboard**
    - Review the **Agent Summary** (active vs. disconnected agents).
    - Check **24-hour alerts summary** (critical, high, medium, low).
    - Note other available dashboards:
        - **Endpoint Security** – Configuration assessment, malware detection, file integrity.
        - **Threat Intelligence** – Threat hunting, vulnerability detection, MITRE ATT&CK mapping.
        - **Security Operations** – SOC-focused data.
        - **Cloud Security** – Cloud-specific alerts.
2. **Go to Explore → Discover**
    - Select the **index pattern**: `wazuh-archives-*`.
    - Confirm **timestamp** is the time field.
    - Refresh field list to decode new Sysmon fields after adding events.
3. **Adjust the Time Range**
    - Use **relative time** (e.g., last 12 hours).
    - Click **Update** to refresh search results.

---

## **3. Searching for Sysmon Event ID 1**

1. **In the search bar**, run:
    
    ```
    data.win.system.eventID:1
    
    ```
    
2. **Expected Result** – None found → **Telemetry gap identified**.

---

## **4. Enabling Sysmon Event ID 1 (Process Creation)**

1. **RDP into the Assumed Breach Host**.
2. Open **`base_sysmonconfig.xml`**.
3. Locate the `<Include>` filter for `powershell.exe`.
4. Replace with an **empty `<Exclude>`** tag:
    
    ```xml
    <Exclude />
    
    ```
    
    - This logs **all process creation events**.
5. **Save the configuration**.
6. **Update Sysmon config via PowerShell (Admin)**:
    
    ```powershell
    cd Downloads
    .\Sysmon64.exe -c .\base_sysmonconfig.xml
    
    ```
    
    - Look for confirmation: schema validated, config updated.

---

## **5. Verifying Event ID 1 Logging in Wazuh**

1. Return to **Wazuh → Discover**.
2. Re-run the query for **event ID 1**.
3. Confirm process creation events are now present.
4. Expand an event and verify:
    - `agent.id`, `agent.ip`, `agent.name`
    - **`data.win.eventdata.CommandLine`** contains executed commands.

---

## **6. Generating a Test Event**

1. **In Mythic**, establish a new callback:
    - On Kali attack host:
        
        ```bash
        cd Downloads
        python3 -m http.server 8000
        
        ```
        
    - On assumed breach host:
        - Run `goodbye_AMSI.exe` to load Apollo agent.
2. **In Mythic**, run:
    
    ```
    shell net localgroup administrators
    
    ```
    
    *(Correct typos as needed.)*
    
3. **In Wazuh**, search for:
    
    ```
    data.win.system.eventID:1 AND data.win.eventdata.CommandLine:*localgroup*
    
    ```
    
4. Confirm matching events appear, including errors and correct commands.

---

## **7. Searching for Sysmon Event ID 3 (Network Connections)**

1. Query:
    
    ```
    data.win.system.eventID:3
    
    ```
    
2. **Expected Result** – None found → **Telemetry gap identified**.

---

## **8. Enabling Sysmon Event ID 3 (Network Connections)**

1. **In Wireshark**, verify beacon traffic:
    - Filter: `http`
    - Note beacon traffic to attack infrastructure (port 80).
2. **Edit Sysmon Config** to include specific destination ports:
    
    ```xml
    <NetworkConnect onmatch="include">
        <DestinationPort>80</DestinationPort>
        <DestinationPort>135</DestinationPort>
        <DestinationPort>443</DestinationPort>
        <DestinationPort>445</DestinationPort>
    </NetworkConnect>
    
    ```
    
3. **Save config**.
4. **Update Sysmon config**:
    
    ```powershell
    .\Sysmon64.exe -c .\base_sysmonconfig.xml
    
    ```
    

---

## **9. Verifying Event ID 3 Locally**

1. Open **Event Viewer** → `Applications and Services Logs` → `Microsoft` → `Windows` → `Sysmon` → `Operational`.
2. Filter for **Event ID 3**.
3. Confirm connections for the selected ports.

---

## **10. Testing and Viewing in Wazuh**

1. Start a **new beacon** with `goodbye_AMSI.exe`.
2. In Wazuh, search for:
    
    ```
    data.win.system.eventID:3
    
    ```
    
3. Confirm network connection logs appear.
4. Expand an event to view:
    - Destination port.
    - Source/destination IP.
    - Associated process image.

---

## **11. Conclusion & Next Steps**

- **Telemetry Gaps Identified**:
    - Process creation events (ID 1) – **Fixed**.
    - Network connection events (ID 3) – **Fixed**.
- **Data Now Available**:
    - Process command lines from executed commands.
    - Network connections from beacon traffic.
- **Next Lecture Preview**:
    - Enable **Sysmon Event ID 7** to detect untrusted code execution.
    - Combine with Event ID 3 for **high-fidelity beaconing detection**.

</section>
</div>
