---
layout: default
title: "Configure Firewall Rules"
permalink: /solo-purple-teaming/configure-firewall-rules/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Configure Firewall Rules</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

# Introduction

In this exercise, you’ll configure **port forwarding rules** on the **Attack LAN pfSense edge device** so that external systems can reach key services running on your Kali host (Mythic C2, SSH, etc.).

---

## **1. Ports We’ll Configure**

We’ll forward the following ports to the Kali host:

| **Port** | **Purpose** | **Forward To** |
| --- | --- | --- |
| **80** | Mythic C2 listener | Kali host |
| **22** | SSH | Kali host |
| **7443** | Mythic Web UI | Kali host |
| **8000** | Python HTTP server for payload downloads | Kali host |
| *(Optional)* **3389** | RDP to Windows RE host | Windows RE host |

---

## **2. Identify the Edge Device IPs**

- **WAN IP** (external-facing, used by outside hosts): `192.168.100.101`
- **LAN IP** (internal-facing, gateway for Attack LAN): `10.0.3.1`

---

## **3. Access the pfSense Web Configurator**

1. From your Kali Linux VM, open a browser and navigate to:
    
    ```
    https://10.0.3.1
    
    ```
    
2. If this is your first login, **change the default password**.
3. If you ever lose access, you can reset the password from the pfSense console:
    - **Select Option 3** to reset to default credentials.

---

## **4. Allow Private IP Forwarding**

Since we’re simulating the internet inside our lab, we need the WAN interface to accept private IPs.

1. In pfSense, go to:
    
    ```
    Firewall → Rules → WAN
    
    ```
    
2. Click the **gear icon** on the WAN interface rule.
3. Scroll down and **uncheck**:
    - *Block private networks and loopback addresses*
4. **Save** and **Apply Changes**.

---

## **5. Create the SSH Port Forward Rule (Port 22 → Kali)**

1. Go to:
    
    ```
    Firewall → NAT → Port Forward
    
    ```
    
2. Click **Add**.
3. Configure:
    - **Interface:** WAN
    - **Protocol:** TCP
    - **Destination Port Range:** SSH (22)
    - **Redirect Target IP:** `10.0.3.2` (Kali host)
    - **Redirect Target Port:** SSH (22)
    - **Description:** `SSH to Kali host`
4. **Save** and **Apply Changes**.

---

## **6. Test SSH Access**

1. On Kali, start SSH:
    
    ```bash
    sudo service ssh start
    
    ```
    
2. From an **external host** (e.g., your Mac), run:
    
    ```bash
    ssh jglo@192.168.100.101
    
    ```
    
    - Replace `jglo` with your Kali username.
3. When prompted, enter your password.
4. Verify you’re on Kali:
    
    ```bash
    hostname
    ip a | grep INET
    
    ```
    
    - You should see `10.0.3.2` among the IPs.

---

## **7. Create the Mythic Web UI Rule (Port 7443 → Kali)**

1. In **pfSense**:
    
    ```
    Firewall → NAT → Port Forward → Add
    
    ```
    
2. Configure:
    - **Interface:** WAN
    - **Protocol:** TCP
    - **Destination Port Range:** Custom → 7443
    - **Redirect Target IP:** `10.0.3.2` (Kali host)
    - **Redirect Target Port:** 7443
    - **Description:** `Mythic UI`
3. **Save** and **Apply Changes**.

---

## **8. Test Mythic Web UI Access**

1. Ensure Mythic is running on Kali.
2. From an **external host**, go to:
    
    ```
    https://192.168.100.101:7443
    
    ```
    
3. You should see the Mythic login page.

---

## **9. Other Recommended Port Forwards**

Repeat the above steps for:

| **Port** | **Forward To** |
| --- | --- |
| 8000 (HTTP payload server) | `10.0.3.2` (Kali) |
| 3389 (RDP) | Windows RE host IP |

---

## **10. Challenge for Students**

Before moving on:

- Configure and **test** all necessary port forwards.
- At minimum, ensure:
    - SSH (22) works.
    - Mythic Web UI (7443) works.
    - Payload HTTP server (8000) works.
    - *(Optional)* RDP (3389) works.

Once complete, you’ll be ready to **set up the breach point** on the Ecoin network for **Attack Level 0**.

</section>
</div>
