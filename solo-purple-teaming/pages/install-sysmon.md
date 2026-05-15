---
layout: default
title: "Install Sysmon"
permalink: /solo-purple-teaming/install-sysmon/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Install Sysmon</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **1. Lab Recap**

![image.png](Install%20Sysmon/image.png)

Before we start installing Sysmon, here’s where we are in the lab:

- We created a new LAN called **AllSafe**.
- Installed a **Wazuh server** inside the AllSafe LAN.
- Installed the **Wazuh agent** on the assumed breach host.
- Configured **port forwarding** on the AllSafe edge device to allow agent traffic from the assumed breach host to reach the Wazuh server manager.

---

### **2. Download Sysmon**

1. **Connect to the assumed breach host** via Remote Desktop.
2. Open a browser and search for **“Sysmon download”**.
3. Navigate to the official **Microsoft Sysmon download page**.
4. Download the latest version (at the time of recording: **Sysmon v15.15**).

![image.png](Install%20Sysmon/image%201.png)

1. Locate the downloaded file in your **Downloads** folder.
2. **Extract the archive**:
    - Right-click the downloaded ZIP file → **Extract All** → Confirm extraction.
    - You should now see the Sysmon binaries in the extracted folder.

---

### **3. Download the Base Configuration**

1. Go to the **GitHub page** associated with this course (Sysmon Modular repository).
2. Locate the **base Sysmon config** file.
3. Click the config file to open it, then click **Download**.
4. If prompted by the browser, click **Keep** to save it.
5. The base config should now be in your **Downloads** folder.

---

### **4. Modify the Sysmon Configuration**

1. Right-click the downloaded base config file → **Open with Notepad**.
2. Zoom in if needed for readability.
3. **Add a process creation rule** to log PowerShell executions:
    
    ```xml
    <ProcessCreate onmatch="include">
        <Image condition="is">powershell.exe</Image>
    </ProcessCreate>
    
    ```
    
4. Save the file.

---

### **5. Install Sysmon**

1. Open **PowerShell as Administrator** (can use the same admin session from the Wazuh agent install).
2. Navigate to the Downloads folder:
    
    ```powershell
    cd C:\Users\rlynn\Downloads
    ls
    
    ```
    
    (Replace `rlynn` with your username if different.)
    
3. Verify you see:
    - **Sysmon folder** (with binaries)
    - **Base config file**
4. Run the Sysmon installation command:
    
    ```powershell
    .\Sysmon64.exe -i baseconfig.xml -accepteula
    
    ```
    
    (Replace `baseconfig.xml` with your config file’s exact name if different.)
    
5. Confirm installation messages show:
    - Driver installed
    - Service started

---

### **6. Verify Sysmon Logging**

1. Still in the admin session, open **Event Viewer**.
2. Navigate to:
    
    ```
    Applications and Services Logs
        └── Microsoft
            └── Windows
                └── Sysmon
                    └── Operational
    
    ```
    
3. Double-click **Operational** and confirm you see active Sysmon events.

![image.png](Install%20Sysmon/image%202.png)

---

### **7. Lab Checkpoint**

✅ Sysmon is now installed and logging events on the assumed breach host.

Before moving on:

- Ensure Sysmon events are visible in Event Viewer under the **Operational** log.

---

**Next Step:** In the following lecture, we will configure **Wazuh** to collect and process these Sysmon logs using the central configuration file `agent.conf`.

</section>
</div>
