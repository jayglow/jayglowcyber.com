---
layout: default
title: "Building the E Corp Domain Controller"
permalink: /solo-purple-teaming/building-the-e-corp-domain-controller/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Building the E Corp Domain Controller</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **Overview**

![image.png](Building%20the%20E%20Corp%20Domain%20Controller/image.png)

In this lab, we will build the **ECorp Domain Controller** (Ecorp-DC-1) and configure it with:

- **Static IP**: `10.0.1.2`
- **Default Gateway**: `10.0.1.3` (LAN interface of the E Corp Edge device)

We’ll use a Windows Server installation (2019 in this example) and Proxmox for virtualization. The process includes creating a template VM, cloning it for the E Corp DC, configuring network settings, and promoting it to a domain controller.

---

## **Step 1 – Create a Windows Server Template**

1. **Log into Proxmox**
    - You may be using a different virtualization platform; steps will be similar.
2. **Create a New VM**
    - Click **Create VM** (example done on host `PVE3`).
    - **Name**: `DC-template`
    - **ISO Image**: Select Windows Server (2019 used here, but 2022 or 2025 are fine).
    - Add **VirtIO drivers** ISO for storage and network support.
3. **System Settings**
    - Enable **QEMU Agent**.
    - Storage: Local LVM Thin (100 GB disk).
    - CPU: 4 cores.
    - Memory: 4096 MB (4 GB).
    - Network Adapter: Select **Intel** (avoid extra driver installs), **Firewall disabled**.
4. **Install Windows Server**
    - Start VM and press any key to boot from the ISO.
    - Language: English → **Next** → **Install Now**.
    - Edition: Datacenter (Desktop Experience).
    - Accept terms → Custom Install.
    - Load VirtIO storage driver (`amd64\2k19`).
    - Install Windows normally.
5. **Initial Setup**
    - Set Administrator password.
    - Log in to verify installation.
    - Power down VM.
    - Right-click VM → **Convert to Template**.

---

## **Step 2 – Clone Template for E Corp DC**

1. **Clone Template**
    - Right-click the template → **Clone**.
    - Name: `ecorp-dc-1`.
    - Type: **Full Clone**.
    - Wait for cloning to finish.
2. **Start the VM**
    - Log in to Windows Server.

---

## **Step 3 – Configure System Settings**

1. **Rename Computer**
    - Go to **System Properties** → **Change Settings**.
    - Name: `ecorp-dc-1`.
    - Restart when prompted.
2. **Configure Network**
    - Go to **Network & Internet Settings** → **Change adapter options**.
    - Right-click **Ethernet** → Properties → **IPv4**:
        - IP Address: `10.0.1.2`
        - Subnet Mask: `255.255.255.0`
        - Default Gateway: `10.0.1.3`
        - Preferred DNS: `192.168.100.1` (LAN interface on home router)
            
            *Optional Alternate DNS: `8.8.8.8`*
            
    - Disable IPv6.
    - Test connectivity:
        - `ping 10.0.1.3` (E Corp Edge LAN)
        - `ping 10.0.2.4` (E Corp Edge WAN)
        - `ping 10.0.2.3` (Ecoin Edge LAN)
        - `ping 192.168.100.102` (Ecoin Edge WAN)
        - `ping 192.168.100.1` (Home Router)
    - Temporarily connect lab to the internet → `ping google.com` (verify DNS).

---

## **Step 4 – Install Active Directory Domain Services (AD DS)**

1. **Open Server Manager**
    - Click **Manage** → **Add Roles and Features**.
    - **Role-based or feature-based installation**.
    - Select this server → **Next**.
    - Check **Active Directory Domain Services** → Add required features → **Next**.
    - Confirm and **Install**.
2. **Promote to Domain Controller**
    - Click the **Post-deployment Configuration** notification.
    - Choose **Add a new forest**.
    - Root domain name: `ecorp.lab`.
    - Functional level: Windows Server 2016.
    - Keep defaults for DNS and GC; set **Directory Services Restore Mode password**.
    - NetBIOS name: `ECORP`.
    - Review → Install → Allow server to reboot.

---

## **Step 5 – Verify Domain Controller**

1. **Log in to Domain**
    - Use credentials: `ECORP\Administrator`.
2. **Test DNS & Connectivity**
    - Open Command Prompt:
        - `ping google.com` → should return replies.

---

## **Final Checklist Before Moving On**

✅ `ecorp-dc-1` has the correct hostname and IP (`10.0.1.2`).

✅ Gateway and DNS are properly configured.

✅ Can ping internal lab interfaces and external domains.

✅ Domain `ecorp.lab` is fully functional.

---

**Next Lecture Preview**: We will clone the template again to create the **Ecoin Domain Controller**, then configure it as a **child domain** under the E Corp parent domain.

</section>
</div>
