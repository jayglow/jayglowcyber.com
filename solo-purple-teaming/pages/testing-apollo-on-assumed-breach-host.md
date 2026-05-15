---
layout: default
title: "Testing Apollo on Assumed Breach Host"
permalink: /solo-purple-teaming/testing-apollo-on-assumed-breach-host/
---

<link rel="stylesheet" href="/assets/css/solo-purple-teaming.css">

<div class="spt-page">
<a class="spt-back" href="/solo-purple-teaming/">← Back to Solo Purple Teaming</a>
<section class="spt-hero">
<p class="spt-kicker">Solo Purple Teaming</p>
<h1>Testing Apollo on Assumed Breach Host</h1>
</section>
<section class="spt-content">

Owner: Mike Sterrett

# Introduction

### **1. Network Layout Recap**

Before generating the payload, understand your lab network layout:

![image.png](Testing%20Apollo%20on%20Assumed%20Breach%20Host/image.png)

- **Attack LAN**
    - **Hosts:** Kali host, Reverse Engineering host
    - **LAN interface (Attack Edge):** `10.0.3.1`
    - **WAN IP (Attack Edge):** `192.168.100.101`
- **Ecoin LAN**
    - **Host:** Assumed Breach Host (`work-r-lin`)
    - **LAN interface (Ecoin Edge):** `10.0.2.3`
    - **WAN IP (Ecoin Edge):** `192.168.100.102`
    - **Assumed Breach Host IP:** `10.0.2.100`

⚠ **Key Point:** The Ecoin LAN cannot see internal Attack LAN addresses.

Traffic from Ecoin → Attack LAN must go through firewall port forwarding rules.

---

### **2. Verify Security Settings on the Assumed Breach Host**

1. Confirm **you are not connected to the Internet**.
2. Disable the following in **Windows Security**:
    - **Cloud-delivered protection**
    - **Automatic sample submission**
3. Keep **Real-time protection ON** – we are testing a Defender bypass.

---

### **3. Check Firewall & Port Forwarding Rules**

![image.png](Testing%20Apollo%20on%20Assumed%20Breach%20Host/image%201.png)

On the **Attack LAN pfSense**:

- Create port forwarding rules for:
    - **80** (HTTP – Mythic C2 callback)
    - **8000** (Python Simple HTTP Server – payload delivery)
    - **7443** (Mythic Web UI)
    - **22** (SSH – optional)
- **Critical for this test:** ports **80** and **8000** must be working.

---

### **4. Generate Apollo Payload in Mythic**

1. On the **Kali host**, log in to Mythic.
2. Go to **Payloads → Actions → Generate New Payload**.
3. Select:
    - **Type:** Windows
    - **Agent:** Apollo
    - **Format:** Executable (WinXe)
4. Click **Next** – no additional options, bare minimum payload.
5. **Listener:** HTTP
6. **Callback Host:** Use the **Attack Edge WAN IP** → `192.168.100.101`
    - This IP will forward traffic to Kali on port 80 where Mythic listens.
7. Click **Next** and name the payload:
    
    `Apollo_breach_test`
    
8. Click **Create Payload**.
9. Once generated, **download** it to the Kali Downloads folder.
    - Delete any older versions.
    - Rename the new file for clarity.

---

### **5. Host the Payload with Python HTTP Server**

1. In Kali, stop any running server with `CTRL + C`.
2. Start a new server:
    
    ```bash
    python3 -m http.server 8000
    
    ```
    
3. This listens on **port 8000**, which is forwarded from the Attack Edge WAN to Kali.

---

### **6. Download Payload on the Assumed Breach Host**

1. Open a browser on the **Assumed Breach Host**.
2. Enter:
    
    ```
    http://192.168.100.101:8000
    
    ```
    
3. You should see the payload list.
4. Download the new `Apollo_breach_test` executable.
    - Ignore SmartScreen warning (no Internet → SmartScreen not reachable).
    - Keep the file.

---

### **7. Execute the Payload**

1. Open the downloaded file location.
2. Run `Apollo_breach_test.exe`.
3. Switch to Mythic:
    - You should see a **new callback** from the assumed breach host.
4. Hide any old callbacks from previous tests.

---

### **8. End the Callback**

1. In Mythic, select the new callback.
2. Send the **exit** command:
    - `exit` → terminate the agent.
3. On the breach host, the process should terminate.

---

### **9. Clean Up**

1. Move the payload to the Trash.
2. Empty the Trash – never leave payloads on test machines to avoid accidental execution or detection.

---

### **10. Ready for Next Steps**

✅ At this point, you should have:

- A working Apollo payload on the assumed breach host.
- Successful Mythic C2 callback with Defender running.
- Verified port forwarding for payload delivery & C2 traffic.

---

### **Coming Next**

In the **next lecture**, you’ll expand the lab:

- Add **two domain controllers**:
    - **ECorp** (Parent Domain)
    - **ECoin** (Child Domain)
- Create **domain trust** for cross-domain enumeration.
- Add another edge device for E Corp LAN.
- Make the Ecoin DC **dual-homed**:
    - One interface in ECorp LAN
    - One interface in ECoin LAN
- Prepare for **Attack Path Level 0** – Solo Purple Teaming with advanced red/blue tactics.

</section>
</div>
