---
layout: default
title: "Joining Assumed Breach Host to ECOIN Domain"
permalink: /solo-purple-teaming/joining-assumed-breach-host-to-ecoin-domain/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Joining Assumed Breach Host to ECOIN Domain</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

### **Introduction**

In this exercise, you’ll join the assumed breach host (`wrk-rlin`) to the **Ecoin** domain using a newly created domain account. You’ll also troubleshoot common domain join issues.

![image.png](Joining%20Assumed%20Breach%20Host%20to%20ECOIN%20Domain/image.png)

---

## **Step 1 – Review the Network Setup**

Before starting, ensure you understand your lab layout:

- **Host:** `wrk-rlin` (Windows)
- **IP Address:** `10.0.2.100`
- **Domain Controller (Ecoin):** `10.0.2.2`
- **Domain Name:** `ecoin`

---

## **Step 2 – Create a Domain User**

1. Log in to the **Ecoin Domain Controller** in Proxmox.
2. Open **Active Directory Users and Computers**:
    
    `Tools → Active Directory Users and Computers`
    
3. Right-click **Users** → select **New → User**.
4. Enter:
    - **First Name:** Rachel
    - **Last Name:** Lynn
    - **User logon name:** `R.Lin` (which becomes `ecoin\rlin`)
5. Click **Next**.
6. Enter a password.
    - **Uncheck:** "User must change password at next logon"
    - **Check:** "Password never expires"
7. Click **Next**, then **Finish**.
8. Verify that **Rachel Lynn** now appears under the **Users** container.

---

## **Step 3 – Prepare the Assumed Breach Host**

1. Log in to the **wrk-rlin** host.
2. Change the **computer name**:
    - Go to **Control Panel → System → Change settings → Change**.
    - Enter new name: `ecoin-wrk-rlin`.
    - Click **OK** and restart the computer.

---

## **Step 4 – Attempt Domain Join**

1. After restart, go back to:
    - **Control Panel → System → Change settings → Change**
2. Under **Member of**, select **Domain** and enter: `ecoin`.
3. Click **OK**.
4. Enter credentials for `ecoin\rlin`.

---

## **Step 5 – Troubleshoot Domain Join Failure**

If the join fails with “Cannot find domain controller”:

1. Check DNS settings:
    - Go to **Ethernet Adapter Properties → IPv4 Settings**.
    - Change the **Preferred DNS server** to the **Domain Controller’s IP** (`10.0.2.2`).
2. Save and close.
3. Test connectivity:
    - Open Command Prompt.
    - Run:
        
        ```bash
        ping ecoin-dc1
        
        ```
        
    - You should get replies from the domain controller.

---

## **Step 6 – Successfully Join the Domain**

1. Repeat the domain join:
    - **Control Panel → System → Change settings → Change**
    - Enter domain: `ecoin`
    - Provide `ecoin\rlin` credentials.
2. You should now see **"Welcome to the Ecoin domain"**.
3. Restart the computer.

---

## **Step 7 – Log in as the New Domain User**

1. On the login screen, select **Other User**.
2. Enter:
    - Username: `ecoin\rlin`
    - Password: (the one you set in Step 2)
3. You should see **"Welcome Rachel Lynn"** as the profile is created.

---

## **Step 8 – Security Note**

- **Any authenticated user** in the domain can add computer objects to it.
- From an attacker’s perspective:
    - If they have valid domain credentials, they could join a new host to the domain.
    - This allows them to operate from a “bring-your-own-device” (BYOD) machine, avoiding logs from compromised hosts.
    - This technique can help evade detection in a real attack scenario.

---

## **Step 9 – Final Verification**

- Ensure `wrk-rlin` is joined to the Ecoin domain.
- Successfully log in with the `ecoin\rlin` account.

---

✅ **Next Step in the Lab:**

We will configure a vulnerable service for enumeration during the Attack Path Level 0 exercise.

</section>
</div>
