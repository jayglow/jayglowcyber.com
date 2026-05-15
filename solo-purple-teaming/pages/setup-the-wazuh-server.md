---
layout: default
title: "Setup the Wazuh Server"
permalink: /solo-purple-teaming/setup-the-wazuh-server/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Setup the Wazuh Server</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **1. Objective**

In this lecture, we will:

- Create a new server VM.
- Install **Wazuh** on it.
- Configure it to work within the **AllSafe LAN**.
- Prepare it for future lectures where we’ll use Wazuh to monitor endpoints.

---

### **2. Lab Overview**

Our lab diagram now includes a **new LAN** called **AllSafe**.

The Wazuh server will be installed in this LAN and connected via our pfSense Edge device.

![image.png](Setup%20the%20Wazuh%20Server/image.png)

---

### **3. Creating the Wazuh Server VM**

**Steps:**

1. **Select Host in Proxmox**
    - In the example, `PVE4` is used (in a four-host cluster).
    - You can choose any available host in your environment.
2. **Create a New VM**
    - Give it a name (e.g., `wazuh-demo`).
    - Select the ISO: **Ubuntu 24.04 LTS** (other Linux distributions will work as well).
    - Check the **QEMU agent** option (optional but useful).
3. **Assign Resources** (based on Wazuh’s recommendations for 100–125 agents):
    - **Disk**: 100 GB
    - **CPUs**: 4
    - **RAM**: 8 GB (8192 MB)
4. **Disable the Proxmox Firewall** for this VM (optional for lab use).
5. **Finish VM Creation** and **Start** it.

---

### **4. Installing Ubuntu**

1. Launch the VM console.
2. Select **Try or Install Ubuntu**.
3. Go through the setup wizard:
    - Language, time zone, and keyboard layout.
    - **Normal Installation** (for browser and utilities).
    - Erase disk and install Ubuntu.
    - Create a user account and password.
4. Wait for the installation to complete, then **restart** the VM.
5. **Remove the installation ISO**:
    - Hardware → CD/DVD Drive → **Do Not Use Media**.
6. Log in to Ubuntu.

---

### **5. Installing Wazuh**

1. Open a browser and navigate to the **Wazuh Quickstart Installation** documentation.
2. Review system requirements — our VM meets them.
3. Copy the installation command from the documentation.
4. Open a terminal, paste the command, and run it.
    
    *(You will be prompted for your password.)*
    
5. Wait for the installation to complete — this takes several minutes.

---

### **6. Saving Admin Credentials**

- After installation, Wazuh provides an **admin password**.
- **Save this password securely** — it is difficult to recover if lost.

---

### **7. First Login to Wazuh Web UI**

1. In a browser, go to:
    
    ```
    https://localhost
    
    ```
    
2. Accept the **self-signed certificate** warning.
3. Log in with:
    - Username: `admin`
    - Password: (the one provided during install)

---

### **8. Creating a Secondary Admin Account**

1. Navigate to:
    
    **Management → Security → Internal Users**.
    
2. Create a new user account with a memorable password.
3. Assign **admin privileges** to this account.
    
    *(This ensures you can still log in if the original password is lost.)*
    

---

### **9. Assigning a Static IP (AllSafe LAN)**

1. Open **Network Settings** in Ubuntu.
2. Edit the active wired connection.
3. Under **IPv4 Settings**:
    - Change to **Manual**.
    - Address: `10.0.4.2`
    - Netmask: `/24`
    - Gateway: `10.0.4.3` (LAN interface on AllSafe pfSense)
    - DNS: `192.168.10.1` (router interface)
4. Save and reconnect.
5. Verify connectivity:
    - `ping google.com` (external test)
    - `ip a` (check assigned IP)

---

### **10. Testing pfSense Access**

1. In a browser, go to:
    
    ```
    https://10.0.4.3
    
    ```
    
2. Accept certificate warning.
3. Log in (default: `admin` / `pfsense`) and change the admin password.

---

### **11. Setting Up Port Forwarding for Wazuh Access**

1. In pfSense:
    - **Firewall → NAT → Port Forward → Add**
        - Protocol: TCP
        - Destination Port: HTTPS (443)
        - Redirect IP: `10.0.4.2` (Wazuh server)
        - Redirect Port: HTTPS
        - Description: `Wazuh Dashboard`
    - Save and Apply Changes.
2. **Allow Private Networks**:
    - Interfaces → WAN
    - Uncheck **Block private networks** and **Block bogon networks**.
    - Save and Apply.

---

### **12. Accessing Wazuh from Outside**

1. In a browser on another machine, go to:
    
    ```
    https://192.168.100.103
    
    ```
    
    *(WAN interface of AllSafe edge device)*
    
2. Accept certificate warning.
3. Log in to the Wazuh Web UI.

---

### **13. Verification Before Moving On**

Before the next lecture, ensure:

- Wazuh server is **installed and running**.
- You can **ping internal and external addresses**.
- pfSense web configurator is accessible.
- Wazuh dashboard is reachable from your host machine.

---

### **14. Next Steps**

In the following lectures, we will:

- Set up port forwarding for Wazuh agent traffic.
- Install the Wazuh agent on `wrk-r-lin`.
- Forward telemetry to the Wazuh server.
- Take a **crash course on Wazuh**:
    - Building dashboards
    - Setting up detections
    - Analyzing telemetry
- Deploy **Sysmon** and forward its event data to Wazuh.

</section>
</div>
