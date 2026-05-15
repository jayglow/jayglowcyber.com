---
layout: default
title: "Sending Syslog Events From ECOIN Edge"
permalink: /solo-purple-teaming/sending-syslog-events-from-ecoin-edge/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Sending Syslog Events From ECOIN Edge</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **1. Recap of the Lab Setup**

- **AllSafe LAN**: Where the **Wazuh server** resides.
- **Ecoin Edge Device**: The focus for this configuration, forwarding firewall events using **Syslog** to the Wazuh server.
- **Purpose of Syslog in Purple Teaming**:
    - Standardized protocol for sending log data from devices to a central location.
    - Includes source and severity for easy anomaly detection.
    - Allows real-time testing of offensive techniques and refining detection strategies.

![image.png](Sending%20Syslog%20Events%20From%20ECOIN%20Edge/image.png)

---

### **2. Steps Overview**

We will:

1. **Set port forwarding** on AllSafe Edge for Syslog (UDP 514) → Wazuh server.
2. **Configure Syslog forwarding** on Ecoin Edge (pfSense) to send firewall events to the Wazuh server.
3. **Configure Wazuh manager** to accept incoming Syslog events.

---

### **3. Step 1 – Port Forwarding on AllSafe Edge**

1. Log into **AllSafe Edge**.
2. Go to **Firewall → NAT** and click **Add New Rule**.
3. **Interface**: WAN
    
    **Protocol**: UDP
    
    **Destination Port**: Syslog (514)
    
    **Redirect Target IP**: `10.0.4.2` (Wazuh Manager)
    
    **Redirect Target Port**: Syslog (514)
    
4. Add a description for clarity.
5. Save and **Apply Changes**.

---

### **4. Step 2 – Syslog Forwarding on Ecoin Edge (pfSense)**

### **A. Install syslog-ng**

1. Log into **Ecoin Edge**.
2. Go to **System → Package Manager → Available Packages**.
3. Search for `syslog-ng`.
4. Install **syslog-ng** (v1.16 at time of setup).
5. Confirm installation.

### **B. Enable syslog-ng**

1. Go to **Services → syslog-ng**.
2. Check **Enable syslog-ng**.
3. Select interfaces to monitor: **WAN** and **Loopback** (use Ctrl-click to select multiple).
4. Protocol: **UDP**.
5. Port: `5140` (default here; will route later).
6. Save.

### **C. Create a Destination**

1. In **syslog-ng**, go to **Advanced → Add**.
2. Name: `Wazuh`.
3. Type: Destination.
4. Parameters:
    
    ```
    udp("192.168.100.103" port(514) localip("10.0.2.3"))
    
    ```
    
    - `192.168.100.103`: WAN IP of AllSafe Edge.
    - `10.0.2.3`: LAN IP of pfSense Ecoin Edge.
5. Save.

### **D. Create a Log Forwarder**

1. Add a new log route: Name it `Wazuh Route`.
2. **Source**: Default.
3. **Destination**: Wazuh.
4. Save.

### **E. Enable Remote Logging**

1. Go to **Status → System Logs → Settings**.
2. Enable **BSD RFC 3164** format.
3. Check **Enable sending log messages to remote syslog**.
4. **Source Address**: LAN interface.
5. **Destination IP**: `192.168.100.103` (AllSafe Edge WAN).
6. **Port**: 514.
7. Select log categories (initially send everything).
8. Save.

---

### **5. Step 3 – Configure Wazuh Manager to Accept Syslog**

1. In Wazuh UI, go to **Server Management → Settings → Edit Configuration**.
2. Add a `<remote>` block:
    
    ```xml
    <remote>
      <connection>syslog</connection>
      <port>514</port>
      <protocol>udp</protocol>
      <allowed-ips>192.168.100.0/24</allowed-ips>
    </remote>
    
    ```
    
    - Allows all devices in `192.168.100.0/24` to send Syslog.
3. Save changes.
4. Restart the Wazuh Manager.

---

### **6. Step 4 – Verification**

### **A. From the Wazuh Server**

1. SSH into the Wazuh server.
2. Run:
    
    ```bash
    tcpdump -i any port 514
    
    ```
    
3. Confirm you see incoming Syslog events from Ecoin Edge.

### **B. From Wazuh UI**

1. Go to **Discover → Index: Wazuh Archives**.
2. Search for `pfSense`.
3. Confirm Syslog entries from Ecoin Edge are present.

![image.png](Sending%20Syslog%20Events%20From%20ECOIN%20Edge/image%201.png)

---

### **7. Step 5 – Optimize Log Forwarding**

- Initially, all logs were forwarded.
- For efficiency, forward **only firewall events**:
    1. In pfSense: **Status → System Logs → Settings**.
    2. Under remote forwarding, select **Firewall Events** only.
    3. Save.

---

### **8. Final Checklist**

✅ AllSafe Edge forwards UDP 514 to Wazuh.

✅ Ecoin Edge sends firewall Syslog events via syslog-ng.

✅ Wazuh Manager receives and parses logs.

✅ Verified via `tcpdump` and Wazuh UI search.

</section>
</div>
