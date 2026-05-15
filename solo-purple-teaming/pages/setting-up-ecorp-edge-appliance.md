---
layout: default
title: "Setting Up ECORP Edge Appliance"
permalink: /solo-purple-teaming/setting-up-ecorp-edge-appliance/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Setting Up ECORP Edge Appliance</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

# **Overview**

In this lecture, we will add the **E Corp edge device** to our lab environment for **Attack Level Zero**.

This will allow traffic to route from the E Corp network → through the E Coin network → into our simulated Internet → and finally, out to the real Internet.

![image.png](Setting%20Up%20ECORP%20Edge%20Appliance/image.png)

## **Lab Design**

- **E Corp** will be the **parent domain**.
- **E Coin** will be the **child domain**.
- We are adding an **edge device** between E Corp and E Coin.

**E Corp Edge Device IP Plan:**

- **LAN IP:** `10.0.1.3`
- **WAN IP:** `10.0.2.4`
- **Upstream Gateway:** LAN interface of the E Coin Edge (`10.0.2.3`)

---

## **Step 1 – Clone and Name the New pfSense Device**

1. In **Proxmox**, we cloned the pfSense template.
2. Named it **ECorp Edge**.
3. By default, the WAN IP may be assigned via DHCP from your home router
    
    (e.g., `192.168.100.106` in this example — yours may differ).
    

---

## **Step 2 – Assign Network Interfaces**

If you forgot to assign a LAN interface during creation:

1. At the pfSense console menu, select **Option 1** (Assign Interfaces).
2. Do **not** set up VLANs → select **No**.
3. Assign:
    - **WAN:** `vtnet0`
    - **LAN:** `vtnet1`
4. Confirm changes.

---

## **Step 3 – Configure the WAN Interface**

1. From the menu, select **Option 2** (Set Interface IP Address).
2. Choose **1 (WAN)**.
3. DHCP: **No**.
4. Set IP: `10.0.2.4`
    
    Subnet: `/24`
    
5. Upstream Gateway: `10.0.2.3` (LAN of E Coin Edge).
6. Default Gateway: **Yes**.
7. IPv6: **No**.
8. Enable DHCP: **No**.
9. Revert to HTTP: **No**.

---

## **Step 4 – Configure the LAN Interface**

1. From the menu, select **Option 2** again.
2. Choose **2 (LAN)**.
3. DHCP: **No**.
4. Set IP: `10.0.1.3`
    
    Subnet: `/24`
    
5. Upstream Gateway: *(leave blank — LAN doesn’t need one)*.
6. IPv6: **No**.
7. Enable DHCP: **No**.
8. Revert to HTTP: **No**.

---

## **Step 5 – Temporary Host Reconfiguration**

Since no host exists on the new E Corp LAN yet:

1. On your **Windows 11 (R-LIN)** assumed breach host:
    - Open **Network & Internet → Ethernet → IPv4 Settings**.
    - Set:
        - IP: `10.0.1.100`
        - Gateway: `10.0.1.3`
        - Preferred DNS: *(leave as is for now)*.
    - Save settings.

---

## **Step 6 – Access the pfSense WebConfigurator**

1. In a browser, go to:
    
    `https://10.0.1.3`
    
2. Accept the certificate warning → Continue.
3. Login with:
    - **Username:** `admin`
    - **Password:** `pfsense`
4. Change the admin password to something secure.

---

## **Step 7 – Allow Private Network Routing**

Because we’re simulating the Internet with private IP ranges:

1. Go to **Interfaces → WAN**.
2. Scroll down and **uncheck**:
    - *Block private networks and loopback addresses*.
3. **Save** and **Apply Changes**.

---

## **Step 8 – Connectivity Testing**

From the Windows host, test each hop:

1. **E Corp LAN Interface** → `ping 10.0.1.3`
2. **E Corp WAN Interface** → `ping 10.0.2.4`
3. **E Coin LAN Interface** (Upstream Gateway) → `ping 10.0.2.3`
4. **E Coin WAN Interface** → `ping 192.168.100.102`
5. **Home Router LAN** → `ping 192.168.100.1`
6. **Public Internet Test**:
    - Temporarily connect lab to Internet.
    - `ping google.com` (DNS resolution via home router).

If all tests pass, you have full routing from E Corp → E Coin → simulated Internet → real Internet.

---

## **Step 9 – Before Moving On**

Before the next lecture:

- Ensure the E Corp Edge appliance is **fully configured**.
- You can **successfully ping**:
    - E Corp WAN
    - E Coin LAN
    - E Coin WAN
    - Home Router LAN
    - `google.com`

---

## **Next Lecture Preview**

We will configure the **E Corp Domain Controller** with the IP:

```
CopyEdit
10.0.1.2

```

</section>
</div>
