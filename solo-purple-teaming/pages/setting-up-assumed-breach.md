---
layout: default
title: "Setting Up Assumed Breach"
permalink: /solo-purple-teaming/setting-up-assumed-breach/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Setting Up Assumed Breach</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

# Introduction

In this lecture, we’ll set up the **Assumed Breach Host** for our lab environment. This host will be used to simulate a compromised internal system within the **Ecoin LAN**.

---

## **1. System Requirements**

We will use **Windows 11** with the following specifications:

- **RAM:** 4 GB
- **CPU:** 4 cores
- **Disk Space:** 100 GB

📌 *Tip:* You can search for **Windows 11 Enterprise Evaluation Edition** online to download the ISO for installation. This provides an evaluation period suitable for lab work.

> ⚠️ Since this is an advanced course, the basic Windows installation steps are not covered here.
> 

---

## **2. Network Configuration**

The Windows 11 host will use a **static IP** in the Ecoin LAN.

- **IP Address:** `10.0.2.100`
- **Subnet Mask:** `255.255.255.0` (/24)
- **Default Gateway:** `10.0.2.3` (LAN interface of the ecoin edge device)
- **Preferred DNS:** `192.168.100.1` (LAN interface of home router – for Internet DNS resolution)

---

### **Step-by-Step**

1. **Log in** to the Windows 11 Assumed Breach Host as **Administrator**.
2. Open **Network & Internet settings** → **Ethernet**.
3. Locate your Ethernet adapter and select **Edit IP settings**.
4. Switch to **Manual** configuration.
5. Enter the following:
    - **IP Address:** `10.0.2.100`
    - **Subnet mask:** `255.255.255.0`
    - **Gateway:** `10.0.2.3`
    - **Preferred DNS:** `192.168.100.1`
6. Save your changes.

---

## **3. Connectivity Testing**

We will test the configuration from the Windows host.

1. **Open Command Prompt**.
2. **Ping the LAN interface** on the ecoin edge device:
    
    ```bash
    ping 10.0.2.3
    
    ```
    
    You should get a reply.
    
3. **Ping the Attack Network WAN interface**:
    
    ```bash
    ping 192.168.100.101
    
    ```
    
    If this fails, we need to adjust firewall settings on the ecoin edge device.
    
4. **Ping Google** to confirm Internet access:
    
    ```bash
    ping google.com
    
    ```
    

---

## **4. Adjusting pfSense Edge Device Firewall Settings**

If you cannot ping across networks, the edge device may be blocking private network traffic.

1. Open a browser and go to the ecoin edge device LAN interface:
    
    ```
    https://10.0.2.3
    
    ```
    
2. **Accept the security warning** (click *Advanced* → *Proceed*).
3. **Log in** to the pfSense web UI.
4. Navigate to:
    
    **Firewall** → **Rules**
    
5. Locate the option for **RFC 1918 networks** (Block private networks).
6. Click the gear icon to edit the rule and **uncheck**:
    - *Block private networks and loopback addresses*
7. Click **Save** → **Apply Changes**.
8. Re-test your ping to the Attack Network WAN interface.

---

## **5. Final Verification**

Make sure:

- The Assumed Breach Host is in the E**coin LAN**.
- You can ping:
    - The E**coin LAN gateway** (`10.0.2.3`)
    - The **Attack Network WAN interface** (`192.168.100.101`)
    - External domains like **google.com** (if Internet is enabled for the lab)

---

## **6. Lab Best Practice**

- Keep your lab **disconnected from the Internet** unless Internet access is specifically required for a task.
- Always verify gateway and cross-network connectivity before moving to the next phase.

---

✅ **You’re now ready for the next lecture.** Your Windows 11 Assumed Breach Host should be configured, connected to the ecoin LAN, and tested for connectivity both within the lab and to the Internet.

</section>
</div>
