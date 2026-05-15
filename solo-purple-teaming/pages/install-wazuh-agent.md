---
layout: default
title: "Install Wazuh Agent"
permalink: /solo-purple-teaming/install-wazuh-agent/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Install Wazuh Agent</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### 1. **Reviewing the Network Setup**

Before installing the Wazuh agent, let’s recap the current lab network configuration:

- **AllSafe LAN**
    - Network: `10.0.4.0/24`
    - Wazuh Server resides here.
- **Ecoin LAN**
    - The **assumed breach host** (`wrk-r-lin`) is here.
    - IP address: `10.0.2.100`.
- **Traffic Path**
    - To reach the Wazuh server, traffic must:
        1. Egress from **Ecoin** through the **Ecoin Edge Device**.
        2. Ingress into **AllSafe** through the **AllSafe Edge Device**.
        3. Pass through port forwarding rules on the AllSafe edge to reach the Wazuh server.

![image.png](Install%20Wazuh%20Agent/image.png)

---

## 2. **Accessing the Wazuh Dashboard**

1. Log in to the **Wazuh dashboard**.
2. Locate and click **"Deploy new agent"** in the top-left corner.

![image.png](Install%20Wazuh%20Agent/image%201.png)

---

## 3. **Configuring the Agent**

1. **Select your operating system** from the list.
2. **Set the Wazuh server IP address**:
    - **Important**: This is **not** the Wazuh server's internal IP.
    - Use the **WAN IP address** of the AllSafe Edge device:
        
        ```
        192.168.100.103
        
        ```
        
3. Leave all other settings at their defaults.

![image.png](Install%20Wazuh%20Agent/image%202.png)

1. Copy the **PowerShell installation command** provided.

![image.png](Install%20Wazuh%20Agent/image%203.png)

---

## 4. **Installing the Agent on the Assumed Breach Host**

1. Connect to the **assumed breach host** (`wrk-rlin`) via **Remote Desktop Protocol (RDP)**.
2. Open **PowerShell as Administrator**.
3. Paste the copied PowerShell command and press **Enter**.

![image.png](Install%20Wazuh%20Agent/image%204.png)

- **Note**: Your lab must have **Internet access** at this point, as the installation downloads the MSI package from:
    
    ```
    packages.wazuh.com
    
    ```
    

---

## 5. **Starting the Wazuh Service**

1. Once installation completes, locate the **service start command**:
    
    ```
    net start WazuhSvc
    
    ```
    
2. Run this command in the same PowerShell window.
3. The service should now be running.

---

## 6. **Verifying Agent Check-In**

1. Return to the **Wazuh dashboard**.
2. Navigate to:
    
    ```
    Agent Management → Summary
    
    ```
    

![image.png](Install%20Wazuh%20Agent/image%205.png)

1. Verify that the **Ecoin wrk-rlin** host appears as **active**.
    - Host IP: `10.0.2.100`
    - Group: Default (can be changed later).

If you see the agent reporting in, your installation and port forwarding setup are working correctly.

![image.png](Install%20Wazuh%20Agent/image%206.png)

---

## 7. **Lab Completion Check**

✅ Wazuh agent is installed on the assumed breach host.

✅ Agent is checking in to the Wazuh manager.

---

## 8. **Next Steps**

In the next lecture, you will:

- Install **Sysmon** on the assumed breach host.
- Configure Wazuh to forward Sysmon events to the Wazuh manager for monitoring.

</section>
</div>
