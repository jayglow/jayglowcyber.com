---
layout: default
title: "Establish Foothold in ECOIN"
permalink: /solo-purple-teaming/establish-foothold-in-ecoin/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Establish Foothold in ECOIN</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

## **1. Understanding Initial Access**

In this lab, we will simulate gaining an initial foothold on the **assumed breach host** within the Ecoin network.

![image.png](Establish%20Foothold%20in%20ECOIN/image.png)

**Initial Access** is the stage in the attack lifecycle where an adversary first gains entry into a target environment. This can occur through several methods:

- **Phishing** – Convincing a user to click a malicious link or open a malicious attachment.
- **Valid accounts** – Logging in directly using stolen or leaked credentials.
- **Exploiting public-facing web apps** – Leveraging unpatched vulnerabilities.
- **Infected USB drives** – Delivered through social engineering or dropped in public places.
- **Supply chain compromise** – Modifying legitimate software updates to deliver malicious code.

For this **Level Zero** scenario, we will **simulate** that a user (Rachel Lynn) has been tricked into downloading and executing our malicious payload—either from an email attachment or a malicious website.

---

## **2. Solo Purple Teaming Context**

As a solo purple teamer, it’s important to understand the **Lockheed Martin Cyber Kill Chain**.

When focusing on **Initial Access**, you’re typically working through the first five stages:

**Reconnaissance → Weaponization → Delivery → Exploitation → Installation**

![image.png](Establish%20Foothold%20in%20ECOIN/image%201.png)

**Your goal** (both as attacker and defender) is to:

- Detect delivery attempts.
- Block exploitation where possible.
- Alert on suspicious installation activity.

Practicing these stages in a lab helps you improve detection and response throughout the attack chain.

---

## **3. Lab Setup and Scenario**

We will use **Microsoft Remote Desktop** to log into the **assumed breach host** (WRK-R-LIN) as **Rachel Lynn**.

Rachel Lynn will:

1. Visit a malicious website under our control.
2. Download the **Goodbye AMSI** payload.
3. Execute it.

**Important:** Before running the payload, **disconnect from the Internet** to prevent it from being automatically submitted to antivirus vendors. This preserves the payload’s effectiveness for testing.

---

## **4. Downloading the Payload**

1. On the assumed breach host, open a web browser.
2. Navigate to:
    
    ```
    http://192.168.100.101:8000
    
    ```
    
    - This IP is the **WAN interface of our Attack LAN**, forwarded to the Kali host running our staging server.
3. Locate the file **Goodbye_AMSI** in the directory listing.
4. Download the file to the local system.

---

## **5. Executing the Payload**

1. Navigate to the downloaded file location.
2. Double-click **Goodbye_AMSI.exe**.
3. You may see **SmartScreen is unavailable** (because of no Internet connection).
    - This is expected—click **Run**.
4. For this Level Zero lab, a console window will appear.
    - In a real-world scenario, you would configure this to be hidden for stealth.

---

## **6. Verifying Callback in Mythic**

1. In Mythic, go to the **Callbacks** tab.
2. Confirm you see a new callback from:
    - **Username:** `ecoin\rlyn`
    - **IP Address:** `10.0.2.100` (the static IP of WRK-R-LIN in the Ecoin LAN)
3. Click the callback entry to open a **Callback Tab**.
4. Rename the tab to something descriptive, e.g.:
    
    ```
    Assumed Breach Host – RLIN
    
    ```
    

---

## **7. Testing Agent Interactivity**

1. With the callback tab open, try running the `whoami` command:
    - If the command isn’t loaded yet, Mythic will compile and load it first.
2. Once available, execute `whoami`.
3. You should see:
    
    ```
    ecoin\rlyn
    
    ```
    
    for both **Local Identity** and **Impersonation Identity**.
    

This confirms we have an **interactive foothold** inside the Ecoin domain.

---

## **8. Summary & Next Steps**

At this point, you should have:

- Simulated a successful **Initial Access** via malicious download.
- Verified an **active Mythic callback** from the assumed breach host.
- Tested basic **agent interactivity** with `whoami`.

**Before moving on:**

- Ensure the callback is stable and responding.
- Confirm the IP and user match the expected target in your lab diagram.

**Next lecture:** We will perform **host and domain enumeration** to generate telemetry and begin building detections for the Ecoin environment.

</section>
</div>
