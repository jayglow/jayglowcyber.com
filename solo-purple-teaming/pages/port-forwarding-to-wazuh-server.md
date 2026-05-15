---
layout: default
title: "Port Forwarding to Wazuh Server"
permalink: /solo-purple-teaming/port-forwarding-to-wazuh-server/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Port Forwarding to Wazuh Server</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **Objective**

Set up port forwarding on the **AllSafe Edge** device to allow a Wazuh Agent installed on the **Assume Breach Host (wrk-rlin)** to communicate with the **Wazuh Manager**.

---

### **1. Lab Network Context**

![image.png](Port%20Forwarding%20to%20Wazuh%20Server/image.png)

- **Agent Host**: `wrk-r-lin` → `10.0.2.100` (Ecoin LAN)
- **Wazuh Server**: `10.0.4.2` (AllSafe LAN)
- **Network Flow Requirement**:
    1. Traffic from the agent must pass through the **Ecoin Edge** device.
    2. Then, it enters through the **AllSafe Edge** device.
    3. Finally, it is forwarded to the Wazuh server.

---

### **2. Access the AllSafe Edge Device**

1. Log in to the **Wazuh server**.
2. From there, open a web browser and log into the **AllSafe Edge** device (`LAN IP: 10.0.4.3`).
3. Navigate to:
    
    **Firewall → NAT Rules**.
    

---

### **3. Required Ports for Wazuh**

We need to forward **three specific ports**:

| Port | Protocol | Purpose |
| --- | --- | --- |
| 1514 | TCP/UDP | Wazuh Agent traffic |
| 1515 | TCP | Wazuh Remote Install |
| 55000 | TCP | Wazuh REST API |

---

### **4. Creating NAT Rules**

![image.png](Port%20Forwarding%20to%20Wazuh%20Server/image%201.png)

### **Rule 1 – Port 1514**

1. Click **Add**.
2. **Protocol**: TCP/UDP.
3. **Destination Port**: `1514`.
4. **Redirect Target IP**: `10.0.4.2` (Wazuh server).
5. **Redirect Target Port**: `1514`.
6. Save and Apply.
7. **Verify**: Confirm `1514` is mapped to `10.0.4.2:1514`.

![image.png](Port%20Forwarding%20to%20Wazuh%20Server/image%202.png)

---

### **Rule 2 – Port 1515**

1. Click **Add**.
2. **Protocol**: TCP.
3. **Destination Port**: `1515`.
4. **Redirect Target IP**: `10.0.4.2`.
5. **Redirect Target Port**: `1515`.
6. Save and Apply.
7. **Verify**: Confirm `1515` is mapped to `10.0.4.2:1515`.

![image.png](Port%20Forwarding%20to%20Wazuh%20Server/image%203.png)

---

### **Rule 3 – Port 55000**

1. Click **Add**.
2. **Protocol**: TCP.
3. **Destination Port**: `55000`.
4. **Redirect Target IP**: `10.0.4.2`.
5. **Redirect Target Port**: `55000`.
6. Save and Apply.
7. **Verify**: Confirm `55000` is mapped to `10.0.4.2:55000`.

![image.png](Port%20Forwarding%20to%20Wazuh%20Server/image%204.png)

---

### **5. Final Verification**

- In the **Firewall NAT Rules** list, you should see all three port forwarding rules pointing to the Wazuh server (`10.0.4.2`) with the correct ports.
- Ensure each rule is enabled and applied.

![image.png](Port%20Forwarding%20to%20Wazuh%20Server/image%205.png)

---

### **6. Next Steps**

With port forwarding configured, the Wazuh Agent on the Assume Breach Host will be able to communicate with the Wazuh Manager.

In the next phase, we will **install the Wazuh Agent** on `wrk-r-lin` and verify connectivity.

</section>
</div>
