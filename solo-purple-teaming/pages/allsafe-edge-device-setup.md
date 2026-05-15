---
layout: default
title: "AllSafe Edge Device Setup"
permalink: /solo-purple-teaming/allsafe-edge-device-setup/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>AllSafe Edge Device Setup</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **Objective**

In this lab, you will configure the **AllSafe Edge Device** in pfSense with the correct WAN and LAN IP addresses, verify connectivity, and prepare the environment for building the **Wazuh SIEM server** in the next step.

---

## **1. Review the Network Diagram**

![image.png](AllSafe%20Edge%20Device%20Setup/image.png)

From the diagram:

- **WAN Interface IP:** `192.168.100.103`
- **LAN Interface IP:** `10.0.4.3`

The WAN connects to the home network, and the LAN connects to the AllSafe internal network where the Wazuh server will be located.

---

## **2. Start the AllSafe Edge Device**

1. In **Proxmox** (or your virtual lab platform), clone your **pfSense template**.
2. Power on the cloned VM for the AllSafe Edge device.
3. Open the **Console** for this VM.

---

## **3. Initial pfSense Console Access**

When the pfSense console main menu appears, you’ll see that the WAN interface has obtained a **DHCP IPv4 address** from your home LAN (e.g., `192.168.100.106`).

We will replace this with the static WAN IP from the diagram.

---

## **4. Configure the WAN Interface**

1. At the menu, type `2` to **Set interface(s) IP address**.
2. Select the **WAN** interface.
3. When prompted for DHCP, type `n` (No).
4. Enter the WAN IP:
    
    ```
    192.168.100.103
    
    ```
    
5. For the subnet bit count, enter:
    
    ```
    24
    
    ```
    
    (This corresponds to `255.255.255.0`.)
    
6. For the upstream gateway, enter:
    
    ```
    192.168.100.1
    
    ```
    
    *(This is the LAN interface of your home router.)*
    
7. When asked about IPv6 configuration, type `n` (No).
8. Skip any additional IPs by pressing **Enter**.
9. When asked to enable DHCP on this interface, type `n`.
10. When prompted to revert to HTTP, type `n`.

---

## **5. Configure the LAN Interface**

1. From the interface configuration menu, select **LAN**.
2. Type `n` for DHCP.
3. Enter the LAN IP:
    
    ```
    10.0.4.3
    
    ```
    
4. For the subnet bit count, enter:
    
    ```
    24
    
    ```
    
5. Leave the upstream gateway blank and press **Enter** (LAN doesn’t require one).
6. Type `n` for IPv6.
7. Press **Enter** to skip additional IPs.
8. Type `n` for enabling DHCP on LAN.
9. Type `n` for reverting changes.

---

## **6. Verify Network Connectivity**

1. From the main pfSense console menu, type `7` to **Ping host**.
2. First, test connectivity to your home router:
    
    ```
    192.168.100.1
    
    ```
    
    - You should receive replies.
3. Next, test Internet connectivity by pinging Google:
    
    ```
    google.com
    
    ```
    
    - A successful reply confirms both WAN and DNS are functioning.

---

## **7. Lab Completion Check**

Before moving on:

- Confirm the AllSafe Edge Device **WAN** IP is `192.168.100.103`.
- Confirm the **LAN** IP is `10.0.4.3`.
- Verify you can **ping the home router** and **google.com** from pfSense.

---

## **Next Steps**

In the upcoming lecture, you will:

- Build and configure the **Wazuh server** inside the AllSafe LAN.
- Test full network connectivity between devices.
- Install the Wazuh EDR agent on `wrk-r-lin` to forward events to the SIEM.

</section>
</div>
