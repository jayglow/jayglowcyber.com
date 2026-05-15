---
layout: default
title: "Moving Kali To Attack LAN"
permalink: /solo-purple-teaming/moving-kali-to-attack-lan/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Moving Kali To Attack LAN</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

# Introduction

In this lesson, you will reconfigure your Kali host so it connects to the **Attack LAN** with a static IP. This allows your Kali box to communicate with your pfSense attack LAN gateway and, if needed, access the Internet.

---

### **1. Objective**

- Assign Kali a **static IP**: `10.0.3.2`
- Use **gateway**: `10.0.3.1` (LAN interface of the Attack LAN pfSense appliance)
- Verify connectivity by pinging through multiple hops, ending with an Internet check.

---

### **2. Open Network Settings**

1. **Log in** to your Kali host in Proxmox.
2. **Right-click** the network icon in the system tray.
3. Select **Edit Connections**.

---

### **3. Check Existing Connection**

1. **Double-click** your current `Wired connection 1`.
2. Go to the **IPv4 Settings** tab.
3. Notice it is set to **Automatic (DHCP)**, which currently pulls an IP from your **home LAN**.
4. We will keep this profile but **add a new one** for the lab.

---

### **4. Create a New Network Profile**

1. In the **Edit Connections** window, click the **+** button.
2. Choose **Ethernet** and click **Create**.
3. Name it something descriptive (e.g., `SuperCyberLab`).
4. Under **IPv4 Settings**:
    - Change **Method** to **Manual**.
    - Click **Add** and enter:
        - **Address**: `10.0.3.2`
        - **Netmask**: `255.255.255.0` (/24)
        - **Gateway**: `10.0.3.1`
5. Click **Save**.

*(Optional: You can disable IPv6 for this profile by setting **IPv6 Method** to "Disable.")*

---

### **5. Switch to the New Profile**

1. **Right-click** the network icon again.
2. Select the **SuperCyberLab** profile.
3. Your Kali host is now connected to the Attack LAN.

---

### **6. Verify the IP Address**

1. Open a terminal.
2. Run:
    
    ```bash
    ifconfig eth0
    
    ```
    
3. Confirm the IP is `10.0.3.2`.

---

### **7. Test Connectivity**

Ping each hop to ensure routing is working:

| Test Target | Command | Expected Result |
| --- | --- | --- |
| **Attack LAN pfSense LAN interface** | `ping 10.0.3.1` | Replies received |
| **pfSense WAN interface** | `ping 192.168.100.101` | Replies received |
| **Home router LAN gateway** | `ping 192.168.100.1` | Replies received |
| **Google.com (Internet)** | `ping google.com` | Replies received |

---

### **8. Fixing DNS Resolution Issues**

If `ping google.com` fails with a **temporary name resolution error**:

1. Go back to **Edit Connections**.
2. Select **SuperCyberLab** and click **Edit**.
3. Under **IPv4 Settings**, add your DNS server (example: your home router gateway `192.168.100.1`).
4. Save and close.
5. Retry:
    
    ```bash
    ping google.com
    
    ```
    
6. You should now get replies.

> Note: In the final lab setup, DNS may be handled by your domain controller in the simulated enterprise environment.
> 

---

### **9. Homework / Lab Task**

Before moving on:

- Reconfigure your **Windows host** to have a static IP in the Attack LAN subnet.
- Set its default gateway to the **pfSense Attack LAN IP**.
- Verify you can:
    1. Ping the pfSense LAN interface (`10.0.3.1`)
    2. Ping the pfSense WAN interface (`192.168.100.101`)
    3. Ping your home router (`192.168.100.1`)
    4. Ping `google.com`

---

### **10. What’s Next**

In the next lecture, you will:

- Configure pfSense firewall rules to forward:
    - **HTTP (80)** → Kali for Mythic C2 listener
    - **8080** → Kali for payload delivery
    - **7443** → Kali for Mythic Web UI access
    - **22** → Kali for SSH access

This will make external access to your Kali C2 setup much easier.

</section>
</div>
