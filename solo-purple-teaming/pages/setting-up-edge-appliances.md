---
layout: default
title: "Setting Up Edge Appliances"
permalink: /solo-purple-teaming/setting-up-edge-appliances/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Setting Up Edge Appliances</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

# 🔧 Setting Up pfSense LAN Edge Devices in Proxmox

## 🎯 Objective

This walkthrough guides you through setting up two pfSense edge routers—one for the **Attack LAN** and one for the **Target LAN (Ecoin)**—using **Proxmox** VMs. Each pfSense VM will serve as a LAN gateway with **static IPs**, proper interface assignments, and Internet connectivity verification.

![image.png](Setting%20Up%20Edge%20Appliances/image.png)

---

## 🖥️ Lab Requirements

- Proxmox environment (PVE)
- pfSense ISO installer (from pfsense.org)
- Two pfSense VMs with:
    - **2 GB RAM**
    - **32 GB disk**
    - **2 network adapters** (WAN & LAN via `vmbr0`)
- Internet access through your home gateway (e.g., `192.168.100.1`)

---

## 📦 Step 1: Download and Upload pfSense ISO

1. Visit: https://www.pfsense.org/download/
2. Select the **Netgate Installer**.
3. Upload the ISO to your **Proxmox local storage** via the Proxmox web interface.

---

## 🧱 Step 2: Create the First pfSense VM

1. In Proxmox, **right-click** your PVE node and select **Create VM**.
2. Name the VM (e.g., `pfSense-edge-template`) and click **Next**.
3. Under OS:
    - Select the **uploaded pfSense ISO**.
4. Under System:
    - Leave defaults.
5. Under Hard Disk:
    - Size: **32 GB** (you can reduce to 25 GB if needed)
6. Under CPU:
    - **1 socket, 1 core**
7. Under Memory:
    - **2048 MB** (can go as low as 1024 MB if resource constrained)
8. Under Network:
    - **Model**: VirtIO (paravirtualized)
    - **Bridge**: `vmbr0`
    - **Firewall**: Unchecked
9. Click **Finish**.

---

## ➕ Step 3: Add Second Network Adapter

1. Select the new VM.
2. Go to **Hardware → Add → Network Device**.
3. Set:
    - **Model**: VirtIO
    - **Bridge**: `vmbr0`
    - **Firewall**: Unchecked
4. Click **Add**.

---

## 💽 Step 4: Install pfSense

1. Start the VM and open **Console**.
2. Follow these steps:
    - Accept the **EULA**.
    - Choose **Install pfSense**.
    - Use default **keymap**.
    - Proceed with **Auto (UFS)** installation (ZFS optional).
    - Select the 32 GB disk.
    - Confirm installation.
    - Choose **Install CE (Community Edition)**.
    - Keep all defaults
    - Reboot after installation.

---

## 📊 Step 5: Assign Interfaces

When prompted:

- **WAN**: `vtNet0`
- **LAN**: `vtNet1`
- Confirm with `Y`.

---

## 🧪 Step 6: Convert to Template and Clone VMs

1. **Shutdown** the VM (`Option 6 → Y`).
2. Convert it to a **template**:
    - Right-click VM → Convert to Template
    - Rename to `pfSense-template` under **Options**
3. Clone it twice:
    - Clone 1 → Name: `attack-edge` (full clone)
    - Clone 2 → Name: `ecoin-edge` (full clone)
4. After cloning:
    - **Eject ISO disk** from each clone under **Hardware**

---

## 🌐 Step 7: Configure `attack-edge` IPs

### Boot `attack-edge`, then:

1. Press `2` to set interface IPs.

### WAN (vtNet0):

- Use **Static IP**: `192.168.100.101`
- Subnet: `24`
- Upstream gateway: `192.168.100.1`
- No DHCP / No IPv6 / No HTTP

### LAN (vtNet1):

- Static IP: `10.0.3.1`
- Subnet: `24`
- No DHCP / No IPv6 / No HTTP
1. Press `7` to ping: `google.com`
    - You should see replies.

---

## 🌐 Step 8: Configure `ecoin-edge` IPs

### Boot `ecoin-edge`, then:

1. Press `2` to set interface IPs.

### WAN (vtNet0):

- Static IP: `192.168.100.102`
- Subnet: `24`
- Gateway: `192.168.100.1`
- No DHCP / No IPv6 / No HTTP

### LAN (vtNet1):

- Static IP: `10.0.2.3`
- Subnet: `24`
- No DHCP / No IPv6 / No HTTP
1. Press `7` to ping: `google.com`
    - You should get successful replies.

---

## ✅ Final Checklist Before Proceeding

| Task | Completed? |
| --- | --- |
| pfSense ISO downloaded and uploaded to Proxmox | ☐ |
| Base pfSense VM created and configured | ☐ |
| Converted to template | ☐ |
| Cloned `attack-edge` and `ecoin-edge` VMs | ☐ |
| Static IPs configured on WAN and LAN interfaces | ☐ |
| Verified Internet access with `ping google.com` | ☐ |

---

## 🧭 What’s Next?

In the next lecture, you’ll:

- Move your **Kali host** and **Reverse Engineering VM** into the **Attack LAN**
- Assign **static IPs** within the `10.0.3.0/24` subnet

Make sure your edge devices are **fully configured and functional** before moving on.

</section>
</div>
